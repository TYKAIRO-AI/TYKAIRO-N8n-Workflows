# Stage 01 — Mindee V2 Connectivity Test

## Purpose

This is the first build stage of TYKAIRO AI Invoice Processing. It verifies that an n8n instance can submit a binary document to Mindee V2, poll the asynchronous job, and retrieve the result.

This is **not** the complete invoice-processing workflow. Gmail intake, invoice-field normalization, validation, duplicate detection, email approval, and Google Sheets persistence are later stages.

## Requirements

- n8n with the built-in Manual Trigger, HTTP Request, Wait, Code, Switch, and Stop And Error nodes.
- A Mindee V2 API key.
- An extraction model configured in the Mindee account.
- Network access from n8n to `api-v2.mindee.net` and the public sample-file host.

## Credentials

Create an n8n **Header Auth** credential:

- Header name: `Authorization`
- Header value: your Mindee V2 API key (do not commit or paste it into node parameters).

Select this same credential in:
- Mindee V2 - Enqueue Extraction
- Mindee V2 - Get Job Status
- Mindee V2 - Get Extraction Result

## Model configuration

In **Mindee V2 - Enqueue Extraction**, replace `REPLACE_WITH_MINDEE_MODEL_ID` with the extraction model ID from your Mindee account. Ensure that model accepts the supplied sample file. The sample is Mindee's public financial-document test image and is intended to test API connectivity, not invoice extraction accuracy.

## Run

1. Import `stage-01-mindee-v2-test.json` into a new n8n workflow.
2. Configure the Header Auth credential on the three Mindee HTTP Request nodes.
3. Set the model ID.
4. Execute **Run Mindee Test**.
5. A successful run should reach **Display Extraction Result** and return the Mindee inference payload.

The workflow polls every 3 seconds and stops after 20 attempts if the job remains in Waiting or Processing. Failed jobs and HTTP request errors terminate with explicit error nodes.

## Expected workflow paths

- Success: enqueue → poll → result retrieval → Display Extraction Result.
- Mindee job failure: Stop - Mindee Processing Failed.
- Poll timeout: Stop - Mindee Polling Timeout.
- HTTP/download error: Stop - HTTP Request Error.

## Security and limitations

- No API keys, customer invoices, or private data are included in this export.
- The sample file is downloaded from Mindee's public test-data repository.
- Polling is suitable for this low-volume connectivity test. Mindee recommends webhooks for higher-volume production processing.
- Do not activate this test workflow as a production invoice intake.
- A successful JSON parse is not equivalent to an n8n import test or a live Mindee execution.

## Test evidence

- JSON syntax parse: passed before commit.
- n8n import test: pending on the target n8n instance.
- Live Mindee test: pending API credential and model ID configuration.
- End-to-end invoice processing tests: not in scope for Stage 01.
