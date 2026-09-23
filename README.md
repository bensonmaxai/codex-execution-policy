# Codex 執行節流規則

版本：2026-09-23 / v1。用途：把已在 Windows 套用的執行原則，局部整併到另一台 Codex。這不是 token 硬上限、插件、服務或自動安裝器。

檔案：POLICY.md 為共用原則；VALIDATION.md 為已驗證範圍及限制。本套件不攜帶任何一台機器的完整全域規則、帳號或設定。

## 給 Mac Codex 的工作單

**Goal**：把 POLICY.md 的核心原則整併到本機既有的工作紀律／執行規則，保留使用者的模型選擇、安全規則、業務流程及工具要求。

**Context**：Windows 已完成兩段局部修改與回讀驗證。效能改善仍待自然任務觀察；取消／預算控制沒有被宣稱為硬性防護。Mac 不能沿用 Windows runtime 的能力結論。

**Constraints**：

- 先定位實際 Codex home 與生效的全域 AGENTS。POLICY.md 是整併參考，不得覆蓋整份 AGENTS；其他段落逐字保留。
- 檢查本機現有規則、必要 preflight 及控制面審查要求；只處理具體衝突，不全面掃描 Skills／Plugins。
- 已有等效規則就沿用。Loop、分工、Skill 呼叫與安全邊界照本機既有政策，不為引用本套件安裝新 Skill。
- 使用者明確要求套用本套件後，才進行局部修改；先備份並核對漂移，寫入後一次回讀與差異核對。
- 不修改模型、config、hooks、排程、Plugins 或認證；不為測試新增 Goal、独立任務或常駐程式，不做外部發送。
- 原生成本查核以本機實際 Desktop runtime 為準；區分介面存在與實測成功。只在現成、隔離、已授權的安全目標存在時取消驗通，否則記未驗證。

**Done when**：指定段落已整併，其他內容保留，差異／備份可核對；回報規則載入證據及成本控制的實際邊界。用後續原本要做的工作觀察成效，不另造測試專案或宣稱已節省固定比例。

## 回復

保存原始段落及差異。需要回復時只反向還原本次改動；如 AGENTS 後續已改過，先比對再整併，不能用旧備份覆蓋整份檔案。

## 發布範圍

此套件由使用者指定發布至公開儲存庫 [bensonmaxai/codex-execution-policy](https://github.com/bensonmaxai/codex-execution-policy)，只提交本目錄三份文件。不要加入 Windows 備份、完整全域 AGENTS、config、原始任務紀錄、帳號資料或本機 runtime 產物。
