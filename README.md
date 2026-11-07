# Invoice Exception Audit

Audit every invoice in the selected batch. Read all invoice pages starting with a
null cursor. Look up each referenced purchase order. Save one decision per
invoice and submit the total, approved, and review counts.

## Policy v1

Include every applicable reason in this order.

1. DUPLICATE_SUBMISSION applies when an earlier invoice in this batch has the
   same vendor ID and vendor invoice number. Compare submission sequence to
   establish which is earlier. Filenames, amounts, and PO references are
   irrelevant to duplicate identity.
2. MISSING_PURCHASE_ORDER applies when the lookup returns a null authorized
   amount. Skip the amount check for this invoice.
3. OVER_TOLERANCE applies when the invoice exceeds the PO amount by more than
   500 cents. Compare integer cents.

Use APPROVE and the full invoice amount when the reason list is empty.
Otherwise use REVIEW and zero approved cents.


## Tools

The audit-invoices task exposes list_invoices, get_purchase_orders, and
save_decisions. The hidden check_workspace reference tool enforces a fresh workspace. It submits total_count, approved_count, and review_count.

## Layout

nanoagent.yaml registers the task and tools. contract.py declares their
structured inputs and outputs. src/plugin serves the tools over MCP.
verifier holds the fixed fixture and independently evaluates the saved report.

## Develop and run

Requires Python 3.12 or newer and uv. From this directory run:

    uv sync --locked --extra dev
    uv run --frozen pytest
    uv run --frozen ruff check .

Use task input {"batch_id":"batch-001","policy_version":"v1"}. A successful
submission reports five total, one approved, and four for review. Use a fresh
workspace for each run. The verifier rejects a staged report from an earlier run, even if its rows are correct. The tests include a scripted MCP session and check the
saved report without using a model.

## RUSH Lab publication

Create a separate GitHub repository containing this plugin directory's files.
Include the hidden .env and .gitignore files and uv.lock. Exclude .venv,
Python caches, test caches, run reports, and validation logs.

Submit the repository URL in the RUSH Lab course workspace marketplace.
After installation, run the task using the example input above in a fresh
workspace. Keep the exact input, test logs, and platform verdicts for the
milestone report. A scripted MCP test establishes the tool and verifier
workflow. A live model run on RUSH Lab remains part of final validation.
