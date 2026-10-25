# HackerRank Orchestrate — October 2026

## Ghost Data Still Exists

An AI agent that audits whether a user's personal data was actually deleted. A deletion workflow may report **Complete** while data still exists in databases, logs, support attachments, exports, search indexes, archives, or backups. Synchronization and restore processes may also bring deleted data back.

For each request, the agent must determine whether deletion was requested, return `COMPLETE`, `INCOMPLETE`, or `NOT_APPLICABLE`, identify remaining data or recreation risks, and produce ordered remediation and verification steps.

Read [`problem_statement.md`](./problem_statement.md) for the complete challenge requirements.

## Quick Start

Run commands from the repository root.

**Linux / macOS**

```bash
python3 code/main.py
```

**Windows PowerShell**

```powershell
python code/main.py
```

The program should read input files from `dataset/` and write the final predictions to the root-level `output.csv`.

If your implementation supports sample mode, run:

```bash
python3 code/main.py --sample
```

Sample mode is for development checks only. It does not replace the required full-dataset run.

## Dataset

The challenge provides the following files in `dataset/`.

| File                           | Description                                                  |
| ------------------------------ | ------------------------------------------------------------ |
| `deletion_requests.csv`        | 518 deletion requests requiring predictions                  |
| `sample_deletion_requests.csv` | Sample deletion requests                                     |
| `sample_output.csv`            | Correct sample answers                                       |
| `user_identities.csv`          | Current user identities                                      |
| `user_audit_log.csv`           | Historical identity changes                                  |
| `system_activity.csv`          | Activity across services and data processes                  |
| `deletion_rules.csv`           | Required treatment for each data category                    |
| `evidence_files.csv`           | Manifest of evidence files                                   |
| `evidence/`                    | Supporting PDFs, images, logs, JSON, text, and SQL snapshots |
| `output.csv`                   | Blank output template                                        |

Only `deletion_requests.csv` requires predictions. The other files provide evidence and context.

## Output Format

The final `output.csv` must contain these columns in this exact order:

```csv
case_id,deletion_status,steps_json
```

### Output requirements

* Produce exactly **518 data rows**, one for every request.
* Sort rows by `case_id`.
* Use only `COMPLETE`, `INCOMPLETE`, or `NOT_APPLICABLE`.
* Store `steps_json` as a valid JSON array.
* Use `[]` for `NOT_APPLICABLE` requests.
* Number steps consecutively from `1` within each case.
* Follow the required phase order: `FINDING`, `REMEDIATION`, `VERIFICATION`.
* Within each phase, follow the operation order specified in `problem_statement.md`, then sort by `system_id` and `resource_id`.
* Findings and completed verification steps must cite appropriate evidence identifiers.
* Proposed remediation and future verification steps for incomplete cases must use an empty `evidence_id` (`""`).

Each step must follow the challenge schema, including:

`step_no`, `phase`, `system_id`, `resource_id`, `operation`, `state`, and `evidence_id`.

A reported workflow status or operation result is a claim, not proof of the current resource state. Assess the available evidence relevant to the request at its `audit_at` timestamp.

## Audit Workflow

The intended workflow is:

1. Determine whether deletion was actually requested and authorized.
2. Resolve user identities using current identity data and historical audit logs.
3. Discover associated resources across the available systems and evidence.
4. Identify remaining records, exposed personal information, active data flows, and restore or recreation risks.
5. Apply the applicable deletion and retention policies.
6. Produce evidence-backed findings.
7. Identify remediation actions required to complete an authorized deletion safely.
8. Include verification steps supported by evidence of the current state.
9. Validate row counts, JSON structure, evidence references, step numbering, and ordering.

## Evaluation Reports

The following reports must be included inside `code/evaluation/`.

### `evaluation_report.md`

Include:

* Case, row, field, and sequence accuracy.
* At least three additional evaluation cases created by you.
* Failures discovered during testing.
* Improvements made to the implementation.

### `usage_report.md`

Document the final full-dataset run, including:

* Model providers and model names.
* Number of model calls.
* Input and output tokens.
* Total and average tokens per request.
* Estimated total and per-request cost.
* Per-model and overall totals when multiple models are used.

Report only metrics that were actually measured and model usage that actually occurred. If the final run uses no external model calls, state that clearly and distinguish it from AI assistance used during development.

Rerun the evaluation and verify all reported metrics before submission.

## Implementation Limitations to Review

The implementation should be reviewed for these potential limitations:

* Some screenshot evidence may be represented by predefined facts rather than a general screenshot-understanding pipeline.
* Some PDF evidence handling may use filename-based matching or evidence-ID fallbacks rather than validating the actual document content.
* Dynamic loading and application of `deletion_rules.csv` must be verified so policy decisions adapt when rules change.
* Broad exception handling should not silently conceal evidence-parsing failures.

Recommended improvements include content-based evidence extraction, dynamic policy loading, explicit parsing errors, and tests for changed policies, missing evidence, historical identities, retained records, replication drift, and restore risks.

Do not claim that a limitation has been resolved unless the change has been implemented and tested.

## Dependencies

Use a Python version supported by your environment. Install the dependencies actually imported by `code/main.py`.

For example, if required:

```bash
python -m pip install pandas pypdf python-dotenv
```

Check the source code before installing optional dependencies. No external model API key should be required for a local deterministic execution path; verify this by running the program without API credentials.

## Package the Submission

Create `code.zip` from the repository root. Include the runnable code, this README, and the evaluation reports.

**Do not include:**

* `dataset/`
* Virtual environments such as `.venv/` or `venv/`
* Build caches such as `__pycache__/`
* Nested archives
* API keys, credentials, or secrets

### Linux / macOS

```bash
zip -r code.zip README.md code \
  -x "*/__pycache__/*" "*/.venv/*" "*/venv/*" \
     "*/node_modules/*" "*/.git/*" "*/.DS_Store" \
     "*.zip" "*.tar" "*.gz" "*.tgz" "*.7z" "*.rar"
```

### Windows PowerShell

```powershell
Compress-Archive -Path README.md, code -DestinationPath code.zip -Force
```

Before submitting, confirm that `code.zip` contains:

```text
README.md
code/main.py
code/evaluation/evaluation_report.md
code/evaluation/usage_report.md
```

### Submission files

Submit these three items through the challenge interface:

1. `code.zip`
2. Root-level `output.csv`
3. Root-level `log.txt` as the chat transcript

Ensure that `log.txt` follows the required `AGENTS.md` transcript format.

## Final Checklist

* [ ] The full-dataset run completed from the repository root.
* [ ] `output.csv` has the exact header and 518 sorted data rows.
* [ ] Every `steps_json` value parses as a JSON array.
* [ ] Step numbers are consecutive within each case.
* [ ] Phase and operation ordering match `problem_statement.md`.
* [ ] Evidence references support findings and completed verifications.
* [ ] Proposed remediation and future verification steps use `evidence_id: ""`.
* [ ] Evaluation metrics and usage figures have been verified.
* [ ] Both evaluation reports are inside `code/evaluation/`.
* [ ] `code.zip` excludes the dataset, environments, caches, and nested archives.
* [ ] `log.txt` follows the required transcript format.
* [ ] No credentials or secrets are included in the submission.
