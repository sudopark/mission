# Delegation Rules — defaults

campaign.md item 15 and work instruction 3-d inherit this table and may only narrow it. Adjustments after measuring actual use are made by revising this file.

## Decision authority table

| Matter | Autonomous | Report after | Prior approval |
|---|---|---|---|
| Implementation method inside a file, private helpers | ● | | |
| Adding or naming test cases | ● | | |
| Creating a file in an in-scope module | | ● | |
| In-scope refactor that passed the refactor gate | | ● | |
| Changing task order | | ● | |
| Changing the commit sequence | | ● | |
| New types, protocols, or indirection layers | | | ● |
| Editing files outside scope (check against the item 9 owned scope; if it belongs to an adjacent DP, escalate as below) | | | ● |
| Dependency, build/test configuration, or CI changes | | | ● |
| Database migrations, public APIs, localization keys | | | ● |

## Escalation rules

| Situation | Handling |
|---|---|
| The exception responses cover it | The executor acts; reported in completion report item 7 |
| No contingency covers it, but it is within intent and scope | Change directive (after-the-fact approval if it is in the report-after tier) |
| A matter in the prior-approval tier | Stop and wait |
| Confirmed that an adjacent DP's owned scope must be encroached on | Immediate report + notification comment on the other DP's issue; stop until the user approves |
| A limit or delegation judgment splits and no rule clause covers it | Immediate report + harness gap record; the user judges |
| The work instruction's end state cannot be reached | Rewrite or stop — the user decides |
| Campaign plan assumption collapses / affects an LOE goal | If campaign.md item 10 (decision points and branches) has a match, trigger it (progress report); if not, escalate to a campaign plan re-review → fix it, then rewrite the order |
