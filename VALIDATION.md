# 驗證範圍

日期：2026-09-23 部署，2026-09-24 驗證彙整。規則採跨平台設計，可供 Windows、macOS、Linux 的 Codex 環境局部整併；目前只有 Windows 已套用並實測，macOS／Linux 尚未部署、未驗證。此記錄不包含私人工作內容或帳號資訊。

## Windows 已確認

- 只修改 Coding Discipline 與 Lean Native Execution，獨立審查後保留實際結果驗證與明確使用者呼叫技能的邊界。
- 套用前核對原檔、備份、候選雜湊；寫入後完整回讀一致。兩段之外的內容逐位元組相同。
- 本次修改前後 config 雜湊相同；沒有安裝框架、hook、服務或排程。
- 同一任務隨後收到更新 AGENTS 指令。此證據不代表其他既有任務都已刷新。
- 目前 Desktop runtime 對應二進位回報 0.155.0-alpha.16.3；該版本可匯出 Goal、token 通知、turn 取消與命令取消的協定 schema。
- 本任務 Goal 讀取與帳號額度唯讀工具成功。rollout_budget 顯示 under development 且停用。

## 隔離實測

- 小型 CSV 修正：新子代理只改指定程式；既有 4 項測試由 2 項失敗變成全過，測試與無關檔案未修改。這是單次合成案例，不是長期節流證明。
- 使用者明確授權一次 1,000-token 測試 Goal。子代理在 772 tokens 後，收到 1,380 tokens／6 秒的原生 budget_limited 訊息並停止實質工作。限制會觸發，但此次超額 380 tokens；不是精準硬上限或帳單上限。
- 子代理建立 Goal 後，主控 get_goal 仍為 null；未證明 Goal 能統計或限制整棵工作樹。
- 中斷子代理後，代理變成 interrupted，但它啟動的命令仍在兩秒內增加 4 次心跳，直到約 45 秒自行結束。代理停止沒有連帶取消此命令。
- 另一個由主控持有的 Python PTY 命令，經原生 write_stdin 傳 Ctrl-C，約 17 秒即 KeyboardInterrupt／exit 1；心跳停止且程序已不存在。此結果不能推論所有工具或孫程序都能被同樣取消。
- 兩個心跳程序均已結束，測試代理沒有繼續執行。沒有把 budget_limited 的 Goal 改成目標達成。

## 尚未證實與操作邊界

- 未驗證停止 Desktop 主控是否連帶停止全部子代理／命令。取消有子工作時，必須分別核對已知代理與命令；無法確認就揭露未確認部分。
- 本機 daemon 控制查詢遇到 socket 錯誤 10050；尚未驗證可由該入口控制 Desktop 活躍任務。此錯誤不證明 Desktop 本身沒有停止功能。
- command/exec 的取消識別碼有連線範圍，不能假設新連線能取消另一連線的工作。
- 尚未完成三件自然任務的成效觀察，不宣稱節省百分比或帳單金額。

結論：Windows 工作規則已部署，部分原生停止能力已驗證，完整硬停損尚未提供。規則文字的可移植性不代表所有平台的 runtime 能力相同；macOS／Linux 須自行確認本機能力，不複製 Windows 的驗證結論，也不要為此自動重做合成測試或建立 Goal。

## 觀察方式

只使用原本就要做的工作與既有紀錄：核對實際交付、無效重試、耗時及可取得的用量。保留必要驗收，不能靠提早放棄達到「變快」。不同範圍任務不強算節省比例；缺資料記未知。

## 官方參考

- https://developers.openai.com/blog/rethinking-skills-and-prompts-for-gpt-6-astra
- https://developers.openai.com/api/docs/guides/latest-model
- https://learn.chatgpt.com/docs/config-file/config-reference
- https://learn.chatgpt.com/docs/app-server
- https://learn.chatgpt.com/docs/hooks
