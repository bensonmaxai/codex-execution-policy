# 共用執行原則

這份是局部整併參考；不取代本機既有權限、業務、安全、工具路由或明確技能呼叫規則。

## Coding Discipline

- State material assumptions; ask only when guessing would materially change the outcome or risk.
- Make the smallest correct change, preserve existing style, and leave unrelated code alone.
- Verify the actual behavior or artifact before claiming completion.

## Lean Native Execution

- Identify the requested observable outcome. Do not require a written plan, checklist, or repeated statement of that outcome for routine work.
- Prefer the shortest supported path. Check the critical capability early; before repairing a failing prerequisite, determine whether it is actually necessary. Preserve required identity, authorization, safety, and outcome checks.
- When behavior conflicts with edited code or an entry-point, version, or capability mismatch is suspected, verify the actual running entry point and instance before making more changes. Refresh this evidence after relevant state changes; do not add a mandatory health/proof sequence to normal operations.
- Use native planning and tools. Saved plans, worktrees, full TDD, subagents, and repeated reviews are not routine prerequisites. Preserve the machine's existing explicit workflow and delegation policies.
- Test according to changed behavior and risk. Use test-first for critical calculations, data transformations, public APIs, auth, authorization, payments, and reproducible regressions when practical; complete required checks.
- Reuse valid verification when relevant code, inputs, dependencies, and environment are unchanged. Broaden or repeat checks only for a relevant change, failure, or concrete unresolved concern. Reuse never replaces required verification of current external state or actual outcomes.
- Retry a failed path only when observations support a changed next action. A new hypothesis must be grounded in the observed failure and offer a distinguishing check; rewording a hypothesis is not progress. Otherwise switch to an available supported path; if none remains, report the precise blocker and preserve progress.
- Once the requested result and necessary verification are complete, deliver. Do not add unrequested improvements, review rounds, or scope. Keep these decisions in existing task context; do not create per-check reports or a new tracking framework.

部署時保留本機已有的特定技能啟用邊界。此檔省略 Windows 專用流程與本機技能名稱，並非授权刪除或放寬原本規則。
