HackerRank Orchestrate — October 2026

Ghost Data Still Exists

Build an AI agent that audits whether a user's personal data was actually deleted. A deletion workflow may report Complete while data still exists in databases, logs, support attachments, exports, search indexes, archives, or backups. Sync and restore processes may also bring deleted data back.

For each request, the agent must determine whether deletion was requested, return COMPLETE, INCOMPLETE, or NOT_APPLICABLE, identify remaining data or recreation risks, and produce ordered remediation and verification steps.

Read problem_statement.md for the complete challenge contract.

Quick Start

Run commands from the repository root.

python3 code/main.py

On Windows PowerShell:

python code/main.py

The program should read inputs from dataset/ and write the final predictions to the root-level output.csv.

If your implementation supports sample mode, run:

python3 code/main.py --sample

Sample mode is for development checks only; it does not replace the required full-dataset run.

Dataset

The challenge inputs are in dataset/.

File

Contents

deletion_requests.csv

518 requests requiring predictions

sample_deletion_requests.csv

32 sample requests

sample_output.csv

Correct sample answers

user_identities.csv

Current user identities

user_audit_log.csv

Historical identity changes

system_activity.csv

Activity across services and data processes

deletion_rules.csv

Required treatment for each data category

evidence_files.csv

Manifest of supplied evidence files

evidence/

PDFs, images, logs, JSON, text, and SQL snapshot exports

output.csv

Blank output template

Only deletion_requests.csv requires predictions. The other files provide evidence and context. SQL captures may include primary databases, regional replicas, reporting replicas, and failover replicas.

Output Format

Create output.csv in the repository root with these columns in this exact order:

case_id,deletion_status,steps_json

Requirements:

Produce exactly 518 data rows, one per request.

Sort rows by case_id.

Use only COMPLETE, INCOMPLETE, or NOT_APPLICABLE.

steps_json must be a valid JSON array.

Use [] for NOT_APPLICABLE.

Number steps consecutively from 1 within each case.

Follow the required phase order: FINDING, REMEDIATION, VERIFICATION.

Within each phase, follow the operation order in problem_statement.md, then sort by system_id and resource_id.

Findings and completed verification steps must cite appropriate evidence identifiers.

Proposed remediation and future verification steps for incomplete cases must use an empty evidence_id ("").

Each step must follow the schema in problem_statement.md, including step_no, phase, system_id, resource_id, operation, state, and evidence_id.

A reported workflow status or operation result is a claim, not proof of the current resource state. Assess evidence relevant to the request at its audit_at timestamp.

Intended Audit Workflow

Determine whether deletion was actually requested and whether it is authorized.

Resolve user identities using current identity data and historical audit logs.

Discover associated resources across available systems and evidence.

Identify remaining records, exposed personal information, active data flows, and restore or recreation risks.

Apply the applicable deletion and retention policy.

Produce evidence-backed findings.

List remediation actions required to complete an authorized deletion safely.

Include verification steps only when supported by evidence of the current state.

Validate output rows, JSON structure, evidence references, step numbering, and ordering.

Evaluation Reports

Include both required reports inside code/evaluation/:

code/evaluation/evaluation_report.md — case, row, field, and sequence accuracy; at least three additional evaluation cases; failures found; and improvements made.

code/evaluation/usage_report.md — details of the final full-dataset run, model providers and names, model calls, input and output tokens, total and average tokens per request, and estimated total and per-request cost. Include per-model and overall totals if multiple models are used.

Report only metrics that were actually measured and model usage that actually occurred. If the final run uses no external model calls, state that clearly and distinguish it from any AI assistance used during development. Rerun evaluation and verify all reported metrics before submission.

Implementation Limitations to Review

The implementation should be reviewed for these known risks before submission:

Some screenshot evidence may be represented by predefined facts rather than a general screenshot-understanding pipeline.

Some PDF evidence handling may use filename-based matching or evidence-ID fallbacks rather than validating the document's actual content.

Dynamic loading and application of deletion_rules.csv must be verified; policy decisions should adapt when the rules change.

Broad exception handling should not silently conceal evidence-parsing failures.

Recommended improvements include content-based evidence extraction, dynamic policy loading, explicit parsing errors, and tests for changed policies, missing evidence, historical identities, retained records, replication drift, and restore risks. Do not claim an improvement is implemented unless it has been implemented and tested.

Dependencies

Use a Python version supported by your environment. Install the packages actually imported by code/main.py. For example, if required:

python -m pip install pandas pypdf python-dotenv

Check the source code and runtime requirements before installing optional dependencies. No external model API key should be required for a local deterministic execution path; verify this by running the program without API credentials.

Package the Submission

Create code.zip from the repository root. Include the runnable code, this README, and evaluation reports. Do not include dataset/, virtual environments, build caches, or nested archives.

macOS / Linux

zip -r code.zip README.md code \
  -x "*/__pycache__/*" "*/.venv/*" "*/venv/*" \
     "*/node_modules/*" "*/.git/*" "*/.DS_Store" \
     "*.zip" "*.tar" "*.gz" "*.tgz" "*.7z" "*.rar"

Windows PowerShell

Compress-Archive -Path README.md, code -DestinationPath code.zip -Force

Before submitting, verify that code.zip contains:

README.md
code/main.py
code/evaluation/evaluation_report.md
code/evaluation/usage_report.md

Submit these three items through the challenge interface:

code.zip

Root-level output.csv

Root-level log.txt as the chat transcript

Do not include API keys, credentials, or secrets in source files, reports, prompts, or the transcript.

Final Checklist

Full-dataset run completed from the repository root.

output.csv has the exact header and 518 sorted data rows.

Every steps_json value parses as a JSON array.

Step numbers are consecutive within each case.

Phase and operation ordering match problem_statement.md.

Evidence references support findings and completed verifications.

Proposed remediation and future verification steps use evidence_id: "".

Evaluation metrics and usage figures have been verified.

Both reports are inside code/evaluation/.

code.zip excludes the dataset, environments, caches, and nested archives.

log.txt follows the required AGENTS.md transcript format.
