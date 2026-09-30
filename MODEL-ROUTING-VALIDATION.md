# 原生代理配置實測

日期：2026-09-30（Asia/Taipei）。環境：Windows、`codex-cli 0.159.0`。本頁是去除本機路徑、帳號與原始 session 紀錄後的驗收摘要；設定見 [MODEL-ROUTING.md](MODEL-ROUTING.md)。

## 套用確認

- 此次只修改 worker 的思考度與描述、新增 reviewer 角色，以及 AGENTS 的分工區塊。
- 主代理仍是 Sol Ultra；explorer Sol Low、default Sol High 保留。全域 config、explorer 與 default 的雜湊及修改時間未變。
- 套用前完成獨立唯讀審查、備份與漂移檢查；套用後 TOML 解析、候選位元組回讀與受保護檔案核對通過。
- 公開的四個角色範本與已部署角色內容一致；主 config 和 AGENTS 只提供最小可整併片段。

## 六種 Sol 思考度的有界線測試

固定請求 `gpt-6.1-sol`，分別測 `low`、`medium`、`high`、`xhigh`、`max`、`ultra`。每種思考度各做一次程式產生與一次安全審查，共 12 次呼叫；同一類任務的使用者 prompt 相同。模型本身沒有工具呼叫或再委派，產出的程式由協調端套用未修改的測試驗收。

| 思考度 | 修碼秒數 | 審查秒數 | 兩題 input tokens | 兩題 output tokens | 修碼驗收 | 審查根因 |
| --- | ---: | ---: | ---: | ---: | --- | --- |
| Low | 20.171 | 12.375 | 56,279 | 654 | 16/16 | 5/5，無誤報 |
| Medium | 21.031 | 17.438 | 56,003 | 788 | 16/16 | 5/5，無誤報 |
| High | 26.235 | 27.578 | 56,003 | 1,307 | 16/16 | 5/5，無誤報 |
| XHigh | 58.203 | 41.593 | 56,003 | 2,538 | 16/16 | 5/5，無誤報 |
| Max | 86.609 | 84.109 | 56,003 | 3,841 | 16/16 | 5/5，無誤報 |
| Ultra | 53.812 | 52.234 | 56,103 | 2,610 | 16/16 | 5/5，無誤報 |

用量合計：336,394 input tokens、11,738 output tokens、cached input 為 0。Reasoning tokens 已含在 output，不另加一次。數字只涵蓋這 12 次呼叫，未包含主代理協調、其他測試、重試或本次發布成本，也不是訂閱帳單。

每個思考度／題型只有 **n=1**，沒有完整工具工作流、Ultra 自動委派或長任務穩定性比較。只有請求的模型／思考度設定，未取得服務端逐次解析模型的獨立遙測；不能用此表證明所有工作都應採 Low，或因單次 Max 較慢就宣稱 Max 品質較差。

## 原生工具工作流

另外使用 ephemeral app-server session，實際載入本機角色設定；測試期間僅對該 session 暫停無關 MCP／Plugin 服務，未改持久設定或停用 sandbox。

1. 主代理開 worker。Worker 讀取契約與程式、修改指定的 `ledger.py`，並執行既有 16 項測試。基線失敗，修正後 **16/16 通過**；協調端以同一份未修改測試再驗收，exit 0。
2. 主代理開 reviewer。Reviewer 讀取原始要求、修改前後程式與測試，自己執行 16 項測試，再以非寫入重現檢查另一份故意不安全的存取控制程式。
3. Reviewer 找到 **5/5 已知根因、無誤報**：匿名檢查太晚、admin 跨租戶越權、admin 可讀軟刪除資料、cache key 缺少租戶，以及 cache hit 跳過當次授權與內容狀態檢查。
4. Fixture 只有預期程式改變，其餘六檔雜湊一致，未多出檔案。Worker 有一次 fileChange；reviewer 沒有 fileChange，六次命令均 exit 0。獨立唯讀審查另核對五項發現與受保護檔案。

## 模型與思考度載入

第一輪測試 client 只收集到主代理 metadata。補齊 child ID 收集後，只補跑短的路由與工具 probe，沒有重跑已通過的修碼工作流。

透過實際 child ID 呼叫支援的 `thread/read(includeTurns=false)`，取得下列 runtime 載入值：

| 角色 | model | reasoningEffort | 實際操作 |
| --- | --- | --- | --- |
| 主代理 | `gpt-6.1-sol` | `ultra` | 完成原生子代理協調 |
| worker | `gpt-6.1-sol` | `medium` | 計算程式 SHA-256 並讀取程式，exit 0 |
| reviewer | `gpt-6-astra` | `xhigh` | 讀契約與程式，指出 cache 越權，兩次命令 exit 0 |

Probe 沒有 fileChange 或 fixture 變動，三個 session 均 completed。`Thread.model` 與 `reasoningEffort` 是**載入配置**，不是 API 服務端逐次模型執行遙測；配置核對與行為驗收是分開的證據。Explorer 與 default 此輪只確認設定保持不變，沒有冒稱重測它們的工具表現。

## 限制與警告

- `--strict-config doctor --json`、`plugin list`、`mcp list` 均 exit 0；doctor 可解析並載入配置，但有既有環境警告，沒有把 exit 0 當成全部診斷全綠。
- 兩次 ephemeral app-server 主 session 的既有 Stop hook 都在 30 秒逾時，任務仍 completed；未改 hook，也未據此推論一般桌面聊天必定有同樣問題。
- Reviewer 設為 read-only，實測未寫檔；**未做強制嘗試寫入的拒絕測試**，不宣稱已完整驗證 sandbox 強制效果或所有連接器的外部寫入限制。
- 測試涵蓋本機載入、原生協調、工具修碼與合成審查，未涵蓋所有正式專案、長期品質、外部系統或 macOS／Linux。
- 既有聊天中的代理未重啟；新 session 已載入這組設定，舊聊天的工具清單仍需刷新。
- 本儲存庫公開的是結果摘要與設定範本，沒有包含合成測試 fixture 或原始 traces，因此不是可直接重跑的公開 benchmark 套件。
