# 原生代理配置

[繁體中文首頁](README.md) · [English overview](README.en.md#native-agent-preset-2026-09-30) · [實測紀錄](MODEL-ROUTING-VALIDATION.md)

2026-09-30 已部署的配置如下。這組配置使用 Codex 原生角色檔，不需要額外代理框架、Plugin、排程或常駐服務。

| 角色 | model | model_reasoning_effort | 寫入範圍 |
| --- | --- | --- | --- |
| 主代理 | `gpt-6.1-sol` | `ultra` | 依使用者授權協調、整合與驗收 |
| explorer | `gpt-6.1-sol` | `low` | 指令限制為唯讀；未另設角色 sandbox |
| worker | `gpt-6.1-sol` | `medium` | 只改主代理指定的實作範圍 |
| default | `gpt-6.1-sol` | `high` | 預設分析／審查；明確指派實作才可修改 |
| reviewer | `gpt-6-astra` | `xhigh` | 角色預設 `sandbox_mode = "read-only"`；指令禁止本機與外部寫入 |

`explorer`、`worker`、`default` 未設定角色專屬 sandbox，承接有效的父 session 設定；文字上的唯讀要求與 sandbox 強制限制要分開看。`reviewer` 的 read-only 是角色預設，父 session 的即時權限／sandbox 覆寫可能優先生效，因此部署後須核對 child 的有效權限，不能只看 TOML。Sandbox 也不取代連接器的授權與外部操作邊界。[官方權限與 sandbox 說明](https://learn.chatgpt.com/docs/agent-configuration/subagents#approvals-and-sandbox-controls)

## 何時分工

短小或高度相依的工作直接由主代理完成。只有範圍可分離、主代理同時有工作可做、而且分工有助於速度或品質時，才開原生子代理。

- `explorer`：有界線的查找、盤點、程式路徑與 log 初步定位。
- `worker`：需求清楚、寫入目標與驗收方式明確的實作。
- `default`：模糊需求、跨模組後果、架構判斷與獨立審查；未指派實作時保持唯讀。
- `reviewer`：依風險決定是否使用，特別是安全、授權、金流、資料完整性、遷移或公開 API。提供原始要求、實際 diff 與驗證證據。變更行數多本身不是固定啟用條件。

一個目標維持一個協調者，一個可變目標維持一個寫入者。子代理不得再開代理或獨立聊天；容量上限不是必須用滿的目標。主代理保留最後驗收責任，不能只因子代理顯示完成或測試全過就接受。

## 範本

```text
templates/
  config.fragment.toml       主代理的兩個設定鍵
  delegation.AGENTS.md       可局部整併的分工規則
  agents/
    explorer.toml
    worker.toml
    default.toml
    reviewer.toml
```

角色檔與本次已部署的四份角色設定一致；config 與 AGENTS 範本僅公開相關片段。既有容量設定維持各機原值，範本不另加或放寬限制。

## 局部套用

1. 先確認已安裝 Codex 的版本、有效設定目錄，以及帳號可用模型與思考度。`ultra` 是否可用取決於模型、客戶端與帳號；不能把 API 的 reasoning 清單直接當成 Codex 的能力清單。[官方設定參考](https://learn.chatgpt.com/docs/config-file/config-reference)
2. 找出有效的 `config.toml`、`AGENTS.md` 與個人角色目錄。官方慣例是 `~/.codex/agents/`；repo 範圍的 `.codex/agents/` 是另一種作用域。本範本優先用個人角色目錄，避免為每個 repo 重複建立相同角色。[官方子代理說明](https://learn.chatgpt.com/docs/agent-configuration/subagents)
3. 備份要改的檔案，檢查既有內容。只將 [config.fragment.toml](templates/config.fragment.toml) 的兩個鍵整併到正確的主代理設定層；若已有同名鍵，修改既有值，不追加重複鍵。保留權限、帳號、MCP、Plugin、hooks、容量與其他工作規則。
4. 將 [agents/](templates/agents/) 中的四個檔案套用到有效個人角色目錄。若已有同名角色，先比較再整併，不盲目覆蓋自訂工具或權限。將 [delegation.AGENTS.md](templates/delegation.AGENTS.md) 的分工段落局部整併到有效 AGENTS。
5. 重新解析 TOML 並回讀 diff，確認只有授權項目改變。開新 session，檢查實際角色清單、runtime metadata 與 child 有效 sandbox／權限，不能只以描述或代理自述證明模型已切換。本機驗證使用新鮮子代理上下文；若介面提供 `fork_turns`，依該介面的繼承契約使用 `none` 或合適的有限上下文。
6. 以原本就要做的工作確認工具操作與驗收結果。沿用仍有效的測試，不因採用範本而自動重跑本文的合成測試或建立 Goal。

若模型不可用，回報缺少的能力並保留原本授權界線；不要宣稱 Astra 已跑過或偷偷代換。若回復設定，只還原本次差異；有後續變更時不能以舊備份蓋掉整份檔案。

## 怎麼理解這組選擇

主代理 Ultra 是保留的既有選擇，沒有在這次調整中降為 High。Worker Medium 是明確實作工作的配置；Default High 處理較大的不確定性；Astra reviewer 只在需要獨立高風險審查時使用。

本機六種 Sol 思考度在兩個小型有界線的案例都通過，但樣本很小，沒有證明 Low 適合所有任務，或 Ultra 比其他層級差。這組設定是已實作並驗證的工作配置，不宣稱全域最佳，也不把模型單價換算成整個工作流的固定節省比例。
