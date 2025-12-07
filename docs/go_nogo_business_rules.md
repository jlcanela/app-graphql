Using GoRules (or any decision‑table engine), the GO / NO‑GO for build rules can be expressed as a scorecard + hard “knockout” criteria.[1][2]

Below is a conceptual design you can map pretty much 1:1 to GoRules decision tables.

## Inputs to the decision table

Typical input columns (from your DB / context object):

- `strategyAlignment` (boolean or score 0–100)  
- `businessCaseScore` (0–100, from ROI, NPV, etc.)[3][4]
- `fundingSecured` (boolean)  
- `architectureApproved` (boolean)  
- `securityApproved` (boolean)  
- `complianceApproved` (boolean)  
- `requirementsReadiness` (0–100, e.g. % of critical requirements clearly defined)[5][2]
- `teamCapacityOK` (boolean, key roles staffed and available in window)[2][5]
- `riskLevel` (enum: low, medium, high, critical)  
- `riskMitigationInPlace` (boolean, for high/critical)[4][2]
- `dependenciesResolved` (boolean: critical external deps ready or accepted)  
- `overallReadinessScore` (0–100, optional aggregate you compute before calling GoRules)[3]

## Hard knockout (“must pass”) rules

These are best expressed as top rows with a “first hit” policy: if any matches, you immediately output NO‑GO (or HOLD).

Examples in decision‑table terms:

- If `fundingSecured == false` → `outcome = "NO_GO"`, `reason = "Funding not approved"`[4]
- If `architectureApproved == false` → `outcome = "NO_GO"`, `reason = "Architecture review not passed"`  
- If `securityApproved == false OR complianceApproved == false` → `outcome = "NO_GO"`, `reason = "Security or compliance not approved"`[6][4]
- If `riskLevel == "critical" AND riskMitigationInPlace == false` → `outcome = "NO_GO"`, `reason = "Unmitigated critical risks"`[4]
- If `teamCapacityOK == false` → `outcome = "HOLD"`, `reason = "Key roles or capacity missing"`[5]

These map naturally to GoRules input expressions like `riskLevel == "critical"` etc.[7][8]

## Scoring and thresholds

After knockouts, you can have scoring‑based rules that classify GO vs CONDITIONAL GO vs NO‑GO:

Define thresholds, for example:[3][4]

- `overallReadinessScore >= 80` → “GO” (if no knockout criteria)  
- `overallReadinessScore between 60 and 79` → “GO_WITH_CONDITIONS”  
- `overallReadinessScore < 60` → “NO_GO”

You can either:

- Precompute `overallReadinessScore` in code and feed it as one input; or  
- Compute partial scores per area in the table (e.g., with GoRules expressions summing weighted fields).[9][10]

Example rule rows (hit policy: first):

1. `overallReadinessScore >= 80` → `outcome="GO"`, `minConditions=false`  
2. `overallReadinessScore >= 60 AND overallReadinessScore < 80` → `outcome="GO_WITH_CONDITIONS"`  
3. `overallReadinessScore < 60` → `outcome="NO_GO"`

## Conditional GO (“GO with conditions”)

Conditional GO is important in practice.[3][4]

You can add rules that:

- Detect specific gaps and output both `outcome` and `conditions`:

Examples:

- `overallReadinessScore >= 60 AND overallReadinessScore < 80 AND requirementsReadiness < 80`  
  → `outcome="GO_WITH_CONDITIONS"`, `conditions=["Clarify critical requirements before sprint 1"]`  
- `overallReadinessScore >= 60 AND dependenciesResolved == false`  
  → `outcome="GO_WITH_CONDITIONS"`, `conditions=["Sign contract X and confirm API availability"]`

In GoRules, `conditions` can be an array or nested object in the output schema.[7][9]

## Suggested table structure (business view)

For a single table with “first hit” policy:

Inputs:

- `strategyAlignmentScore`  
- `businessCaseScore`  
- `requirementsReadiness`  
- `overallReadinessScore`  
- `fundingSecured`  
- `architectureApproved`  
- `securityApproved`  
- `complianceApproved`  
- `teamCapacityOK`  
- `riskLevel`  
- `riskMitigationInPlace`  
- `dependenciesResolved`

Outputs:

- `outcome` (GO, GO_WITH_CONDITIONS, NO_GO, HOLD)  
- `reason` (short text)  
- `conditions` (optional array or structured text)  

Hit policy:

- `FIRST` (first matching row decides), with knockout rules first, then scoring/conditional rules, then a default catch‑all row (`outcome="NO_GO"`, `reason="Insufficient data"`)[10][9]

If you share your preferred JSON shape for a GoRules decision table, a concrete schema (inputs/outputs plus a handful of example rows) can be laid out next.

[1](https://www.ntaskmanager.com/blog/go-no-go-decision-in-project-management/)
[2](https://guides.visual-paradigm.com/making-informed-decisions-with-a-go-no-go-checklist-for-agile-projects-a-scoring-approach/)
[3](https://www.breezedocs.ai/posts/go-no-go-decision-template)
[4](https://www.inventive.ai/blog-posts/go-no-go-decision-projects)
[5](https://www.projectmanagertemplate.com/post/exploring-what-is-go-no-go-in-the-project-management-lifecycle)
[6](https://www.growingscrummasters.com/keywords/go/no-go-decision-points/)
[7](https://github.com/gorules/zen)
[8](https://docs.gorules.io/docs/expression-language)
[9](https://gorules.io/components/decision-table)
[10](https://docs.gorules.io/docs/decision-table)
[11](https://openasset.com/resources/how-to-establish-and-strengthen-your-go-no-go-process/)
[12](https://www.manager-go.com/gestion-de-projet/dossiers-methodes/go-no-go)
[13](https://growingagilecoaches.com/keywords/go/no-go-decision/)
[14](https://thethinksters.com/pm-interview-strategy-how-to-nail-go-no-go-decision-questions/)
[15](https://cribworks.co/its-called-a-go-no-go-decision-for-a-reason-master-it-to-win/)
[16](https://unanet.com/blog/expert-advice-how-to-make-the-right-go-no-go-decision)
[17](https://academy.decisionrules.io/rule-types/decision-tables/create-a-simple-decision-table)
[18](https://www.hso.com/whitepaper/automating-gono-go-processes-to-optimize-pursuit-decisions/automating-gono-go-process-to-optimize-pursuit-decisions)
[19](https://www.youtube.com/watch?v=kPDSsdS9LAY)
[20](https://www.npmjs.com/package/@gorules/zen-engine/v/0.7.2)
