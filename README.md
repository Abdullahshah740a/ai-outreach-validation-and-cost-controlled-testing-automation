# AI Outreach Validation and Cost Controlled Testing Automation

This workflow tests an AI outreach process before it is connected to sending systems. It combines lead-level checkpoints, suppression handling, scoring and draft validation with call and spending controls. This version prioritizes business email identities.

> Sanitized workflow export. Deployment testing has not been performed.

## Functionalities

- **Controlled test modes:** Supports synthetic validation, a 20-record historical comparison dry run and a separately gated paid-provider comparison.
- **Lead identity and suppression:** Checks email identities against a persistent ledger, handles duplicate or suppressed records and assigns visible outcomes instead of silently dropping them.
- **Research and scoring stages:** Models research, overall fit, four pathway scores, greeting selection and an initial email with four follow-ups. The default score policy stops below 60 and prioritizes scores above 80.
- **Cost and retry controls:** Tracks provider calls, tokens, estimated cost and attempts. Paid operation requires authorization settings and a positive spend cap; a canary record must be released before wider continuation.
- **Durable evidence:** Writes ledger checkpoints, comparison results, proof events and run summaries to Google Sheets for review and controlled resumption.
- **Comparison without outreach:** Keeps email sending, phone actions, GoHighLevel pushes and Instantly pushes disabled within this comparison workflow.

## Workflow

1. Start the workflow manually and select the locked run mode.
2. For synthetic mode, load six fixture records and the existing ledger and evidence contracts.
3. For historical comparison, read and validate the 20-record input pack without authorizing provider spend.
4. For an authorized paid comparison, initialize the controller and validate the spend and call limits.
5. Persist a checkpoint before each requested provider action; validate the returned result and apply retry or stop rules.
6. Write final comparison outcomes and run evidence, then assert that external sending flags remain false.

## Tools

| Tool | Purpose |
| --- | --- |
| n8n | Manual execution, routing and orchestration. |
| JavaScript | State machine, validation, identity checks, budgets and result shaping. |
| Google Sheets | Ledger, inputs, comparison outputs and evidence storage. |
| Perplexity | Research provider in the authorized paid branch. |
| OpenAI | Configured GPT 4 Turbo provider for scoring and draft comparisons. |

## Repository contents

- [`workflows/workflow-v01.json`](workflows/workflow-v01.json)
- [`workflows/workflow-v02.json`](workflows/workflow-v02.json)
- [`workflows/workflow-v03.json`](workflows/workflow-v03.json)

| Export | Nodes | Original export status | Version notes |
| --- | ---: | --- | --- |
| `workflow-v01.json` | 42 | Inactive | Business email priority version. Uses a valid business email first, with personal email as fallback; comparison validation checks identity aliases for duplicates. |
| `workflow-v02.json` | 42 | Inactive | Personal email priority version from the numbered draft export. Selects a valid personal email first and uses business email as fallback; comparison validation still requires a valid business identity and checks duplicate business aliases. |
| `workflow-v03.json` | 42 | Inactive | Supplied export |

## Setup

1. Import the selected JSON file into a compatible n8n instance. All published exports are inactive.
2. Open each node and reconnect the required n8n credentials. Original credential bindings were removed.
3. Replace `EXAMPLE_*` resource identifiers, `example.com` endpoints, synthetic contacts and deployment-specific settings with your own configuration.
4. Create the required destination sheets/tables, field mappings, views and state-store schemas before writing any records.
5. Rebind Execute Workflow nodes to the corresponding workflows imported into your instance. Original workflow IDs are not portable.
6. Review prompts, scoring criteria, approval rules, spending caps and historical time windows. Keep the existing approval and no-send controls in place.
7. Use synthetic input to verify branches and destination mappings, then enable the appropriate trigger when the configuration is complete.

Credential types referenced by the original export: `googleSheetsOAuth2Api`, `openAiApi`, `perplexityApi`.

## Export limitations and configuration notes

- Inactive draft. The selected mode is a historical comparison dry run, paid calls are unauthorized and the configured vendor spend cap is zero. These settings describe the export, not evidence of a completed live test.
- Inactive draft. The selected mode is a historical comparison dry run with paid calls unauthorized and a zero vendor spend cap. No outreach sending or external CRM push is part of this comparison workflow.
- Inactive draft. Paid-provider operations are a separate gated branch; the existence of research and model nodes does not itself authorize spend or sending.
- Alternate supplied versions are retained together. Review differences before selecting a version; filenames do not imply production maturity.
- Historical proof receipts, identity anchors, hashes and time windows were sanitized. Regenerate evidence in an isolated environment; these files do not constitute live production proof.
- Workflow export state describes the supplied file only; successful live execution has not been verified.
- Embedded client/prospect examples were replaced. Cached execution data, pinned data and credential bindings were removed.
- Resource IDs, URLs and evidence hashes were replaced for publication. Some fixtures and historical recovery scenarios require new test data and regenerated evidence.
- API credentials belong in n8n credential storage or the configured secret store. Do not commit live keys, contact lists or execution receipts.

## Validation

The preparation checks parse the JSON, preserve node counts and check connection targets. Code-node syntax is checked separately without executing the workflow. These checks do not verify provider access, business results or production readiness.
