## Unified Native Delegation

The current main task owns coordination, model routing, integration and final acceptance. Choose execution scope before model; native subagent roles do not configure independent Codex tasks.

- Do short or tightly coupled work directly. Delegate bounded independent scopes when parallelism helps and the main agent has useful work to continue. Give each scope explicit deliverables and separate write targets.
- Use a concise Goal / Context / Constraints / Done when brief. Include the baseline, write scope, dependencies and acceptance evidence; pass only necessary context.
- Keep model and reasoning effort in the canonical role files for explorer, worker, default and reviewer. Use the actual task-visible role configuration. A model name in a prompt is not proof of routing.
- Route explorer to bounded read-only discovery, inventory, search and log triage. Route worker to scoped implementation with clear acceptance criteria. Route default to ambiguous or cross-cutting analysis and independent review, without edits unless implementation is explicitly assigned.
- Use reviewer for independent read-only review of important or high-risk changes when the risk warrants it, not as a mandatory step for every task. Provide original requirements, the actual diff and verification evidence.
- Uncertainty and consequences override task labels. Escalate unclear requirements, destructive potential, security, financial, legal, authorization, payment, migration and public-API risks.
- Prefer fresh or appropriately limited child context when testing role settings. Follow the current tool's inheritance contract and verify runtime metadata before claiming that a selected model and effort ran.
- Keep one coordinator per objective and one writer per mutable target. Native subagents must not spawn further agents or independent chats. The configured capacity is a ceiling, not a target to fill.
- Independent chats require the user's explicit request. Native subagent authorization does not authorize creating or messaging an independent chat.
- Preserve permissions and external-action boundaries. Check the child's effective permissions because live parent overrides can supersede role defaults. Separate worktrees or agents do not isolate shared external systems; a reviewer must not publish, send, transact or change accounts or permissions.
- If a model is unavailable, report the missing capability and preserve the assigned scope. The main agent may handle bounded implementation, but must not claim an unavailable independent reviewer ran. Diagnose role-loading errors instead of inventing fallback success.
- Collect and verify results. Reuse current, reproducible evidence; add checks for changed integration state, concrete gaps or material risks. Completion, a passing test suite or another agent's approval is not final acceptance.
- On failure or cancellation, inspect prior results and verify the stop of known child work. Do not replay external side effects blindly. Recurring monitoring requires an explicit request.
- Keep local progress tracking only when resume or coordination needs it. Return one integrated result with remaining limitations; do not add a new framework, service or schedule for routine delegation.
