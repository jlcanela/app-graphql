For a GO / NO‑GO for build, what is missing is not generic “work tracking” structure but explicit modeling of gate decisions, criteria, approvers, and their outcomes.[1][2]

## 1. Gate and decision entities

Your model has projects, work, and milestones, but no concept of “gate” or “decision record”. Typical additions:

- `gates` (or `project_gates`): one row per formal gate for a project, e.g. `id, project_id, name ('GO/NO GO BUILD'), phase, scheduled_at, actual_at, status`.[3][2]
- `gate_decisions`: the outcome per gate, e.g. `gate_id, decided_at, outcome (go/hold/rework/kill), rationale, created_by`.[4][3]

This lets you persist “we held GO/NO GO for build on this date and chose GO with conditions”.

## 2. Criteria, scoring, and checklists

The business rules you described live as criteria, scores, and checklist items; none of that is modeled yet.

- `gate_criteria_definitions`: reusable criteria like “Architecture approved”, “Funding secured”, “Risks acceptable”, with weight/mandatory flags.[5][3]
- `gate_criteria_results`: per gate, store `gate_id, criterion_id, status (pass/fail/partial), score, comment`.[6][3]

This allows a structured, auditable evaluation rather than ad‑hoc comments.

## 3. Approvers and workflow

You have memberships and assignments, but no “who approves this gate and in what order / rule”.

- `gate_approvers`: `gate_id, user_id, role (sponsor, architect, finance, security), required (bool), sequence`.[7][8]
- `gate_approvals`: `gate_id, approver_id, decision (approve/reject/abstain), decided_at, comment`.[9][7]

That captures multi‑step approval workflows and makes responsibility clear.

## 4. Relation to artifacts and evidence

GO / NO‑GO uses artifacts (business case, architecture doc, risk register, etc.), which are not distinguished from generic attachments.

- Either extend `attachments` with `artifact_type` (business_case, architecture_review, risk_register, etc.) and link them to `gate_id` as well as `project_id`.[10][3]
- Or add a lightweight `artifacts` table and a join `gate_artifacts (gate_id, artifact_id)` to say “this is the evidence package used at this gate”.[11][10]

## 5. Decision history and conditions

Finally, decision history and conditional GOs are not modeled explicitly.

- In `gate_decisions`, add `conditions` (JSON/text) and maybe link follow‑up `work_item_id`s created to satisfy them.[6][4]
- Optionally, a `previous_gate_id` on `project_gates` to model the decision funnel across multiple gates.[12][13]

### Summary table

| Area                      | What you have now                         | What is missing for GO / NO‑GO for build |
|---------------------------|-------------------------------------------|-------------------------------------------|
| Gate identity             | Milestones only                           | `gates` / `project_gates` table per formal decision point. [2][3] |
| Decision outcome          | Work item status, comments                | `gate_decisions` with outcome, rationale, timestamp, owner. [1][4] |
| Criteria & scoring        | None (only generic custom fields)         | Criteria definitions and per‑gate results / scores. [6][5] |
| Approvers & workflow      | Project memberships, assignments           | Approver roles, approval steps, and approvals per gate. [7][8][9] |
| Evidence / artifacts      | Generic `attachments`                     | Typed artifacts linked to gates (business case, architecture, risk). [3][10] |
| History & conditions      | Status history on work items              | Decision history with conditional GO requirements and follow‑ups. [6][4] |

If you want, a next step could be a concrete Mermaid + SQL extension that adds these gate/decision entities while fitting your existing style.

[1](https://www.projectmanagertemplate.com/post/exploring-what-is-go-no-go-in-the-project-management-lifecycle)
[2](https://www.smartsheet.com/phase-gate-process)
[3](https://www.mastt.com/resources/stage-gate-process-template)
[4](https://gensight.com/gates-and-how-to-operate-them/)
[5](https://www.sciencedirect.com/science/article/abs/pii/S0019850103000804)
[6](https://www.inventive.ai/blog-posts/go-no-go-decision-projects)
[7](https://www.atlassian.com/work-management/project-management/approval-process-workflow)
[8](https://www.wrike.com/workflow-guide/approval-workflow/)
[9](https://www.projectmanager.com/blog/approval-workflows)
[10](https://www.mckinsey.com/capabilities/operations/our-insights/make-milestones-matter-with-decision-gatesstage-gates-with-real-teeth)
[11](https://www.france-energies-marines.org/wp-content/uploads/2023/07/DTOceanPlus_D4.2_Stage-Gate_alpha_WES_20200428_v1.0.pdf)
[12](https://www.transparentchoice.com/blog/demand-management-stage-gates)
[13](https://cerri.com/mastering-the-stage-gate-process-a-comprehensive-guide-from-a-to-z/)
[14](https://www.stage-gate.com/blog/the-stage-gate-model-an-overview/)
[15](https://www.ntaskmanager.com/blog/go-no-go-decision-in-project-management/)
[16](https://www.intellectsoft.net/blog/stage-gate-process/)
[17](https://www.projectmanager.com/blog/phase-gate-process)
[18](https://docs.namasoft.com/guide/approvals-system.html)
[19](https://www.smartflow.ie/post/prioritising-workflow-automation-projects)
[20](https://docs.oracle.com/en/cloud/saas/project-management/fawpm/how-do-i-set-up-and-manage-approval-workflows-in-oracle-project-management.pdf)
