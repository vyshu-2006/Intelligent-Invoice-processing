# Architecture

This document explains the high-level architecture of the Intelligent Invoice Processing system, component responsibilities, data flow, and scaling considerations.

Overview
--------
The system is designed around n8n workflows for orchestration and GPT-4 for intelligent extraction and validation. Components are decoupled so each piece can be scaled independently.

Components
----------
- Ingest (n8n trigger): Accepts invoices via email, file upload, or URL.
- Pre-processor: Optional image processing and OCR to produce text where needed (Tesseract or cloud OCR).
- GPT-4 Extraction: Sends the raw text (and optionally OCR JSON) to a GPT-4 prompt that returns structured invoice JSON (fields, line items).
- Validation Engine: Applies business rules (schema checks, totals, tax rules) to the extracted JSON.
- Approvals Module: For flagged invoices, sends a human-in-the-loop approval request (via email/Slack/UI).
- Persistence: Postgres stores invoice records, processing status, and audit logs.
- Worker/Queue: Background workers handle long-running tasks (file uploads, OCR, retries).

Data Flow (simplified)
----------------------
1. Invoice uploaded -> n8n trigger
2. Pre-process (OCR) -> text
3. GPT-4 extraction -> structured JSON
4. Run validation rules on JSON
   - If valid -> persist, send downstream
   - If exceptions -> create task for review and notify approver
5. On approval, update status and persist final data

Text diagram
------------
Ingest -> Preprocessor -> GPT-4 Extractor -> Validator -> (Persist / Approver) -> Downstream Systems

Scaling & Reliability
---------------------
- Make GPT-4 calls asynchronously with rate-limit handling and exponential backoff.
- Use a queue (Redis/RabbitMQ) for heavy tasks and retries.
- Store raw payloads and prompt/response pairs for reproducibility and debugging.
- Use Postgres for transactional state; consider a file store (S3) for raw invoices.

Security & Privacy
------------------
- Strip or redact sensitive data when possible.
- Store API keys in secrets manager or environment variables — never in repo.
- Implement access controls on approval UIs and audit logs.

Extensibility
-------------
- Add connectors in n8n for ERPs (SAP, NetSuite), accounting systems, or custom APIs.
- Add model tuning or few-shot prompt templates for region-specific invoice formats.
