# Interview guide

Use this case to explain engineering decisions and your contribution. Keep the original experience, the public reference design and future improvements distinct.

## A two-minute walkthrough

1. **Problem.** New ingestion requests required repeated manual work around existing ETL: source checks, schema retrieval, mappings, parameters and review preparation.
2. **Experience.** I participated across the full delivery cycle for the legacy database and SAP BW onboarding workflows: technical discussions, planning, design, job invocation and initial rollout. The early version needed debugging and engineering intervention.
3. **Approach.** A standardized Jira request enters a UI. The model reads framework instructions and proposes tools. Existing Databricks utilities discover metadata and generate the artifacts.
4. **Boundary.** Framework code owns loading semantics. The backend owns permissions, state and validation. Engineers own approval and release.
5. **Reference-design improvement.** This public version makes persistence, retries, isolated working copies and cross-repository activation explicit. Those mechanisms should not all be presented as historical implementation facts.
6. **Evidence.** Walk through the synthetic SAP BW example and one failure scenario. Do not claim measured savings or flawless autonomy without evidence.

My contribution spanned the full delivery cycle as part of the team. This does not imply sole authorship of every framework, frontend or infrastructure component.

## Questions worth preparing for

| Question | Answer to explain |
|---|---|
| Why use AI if the workflows are known? | AI interprets requirements and blockers; deterministic code executes established framework operations. A structured form alone may be preferable where interpretation adds no value. |
| Why not generate all the ETL with the model? | Existing generators encode transformation contracts and operational behavior. Reuse reduces variability and makes changes easier to review. |
| What does SKILL.md actually do? | Supplies versioned workflow instructions. It neither executes code nor grants access. |
| Why custom Python rather than an agent framework? | Narrow workflows and explicit control; the trade-off is maintaining persistence, recovery and human pauses ourselves. |
| What happens when the browser closes? | The logical workflow persists. Workers continue independently, and the UI reconnects to stored state. |
| How do you prevent duplicate jobs? | Persist operation identity before submission, use external idempotency and reconcile uncertain results. Do not promise global exactly-once execution. |
| Does SAP BW use one pipeline for every interface? | Each interface has its own named ADF pipeline. The pipeline structure is shared; the interface, target table and FTP source filename differ through parameters. |
| Why two repositories? | Framework artifacts and ADF definitions have separate ownership and deployment histories. Coordinate compatible revisions rather than pretending they merge atomically. |
| What if generation succeeds but publication fails? | Keep verified artifacts and operation records; reconcile existing commits and retry only missing publication steps. |
| What makes a result review-ready? | Deterministic checks, verified remote content, required review links and no unresolved blockers. It is not proof of production ingestion. |
| What would you improve next? | Validate the recovery scenarios, measure first-review acceptance and hands-on effort, then address the most common failure category. |

## Explain limitations without weakening the case

The first two frameworks worked during the author's involvement, with errors and manual intervention. Current behavior of the original platform is not asserted. This repository is an independent architecture reconstruction with proposed improvements, not a current operational diagram.

There is no executable platform or benchmark in this repository. The strength of the case is the concrete process, the decisions and the explanation of failure handling. If asked for implementation evidence, describe only work you can substantiate and use the synthetic walkthrough to illustrate the public design.

## Preparation checklist

Be ready to describe one real onboarding task, one actual failure and the action you personally took. Avoid replacing those recollections with invented incidents from the synthetic example. Know which components you implemented, which you co-designed and which belonged to other engineers.
