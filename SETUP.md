# Setup Guide

This guide walks through setting up the project for local development and basic production patterns.

Prerequisites
-------------
- Git
- Docker & Docker Compose (recommended for dev)
- Node.js (if running helper scripts locally)
- OpenAI API key (or other LLM provider credentials)
- Postgres database (local via Docker Compose or managed)

Files added
-----------
- .env.example — sample environment variables
- docker-compose.yml — example compose file to run n8n + Postgres locally

.env example
------------
Copy `.env.example` to `.env` and fill in values.

Example variables in .env:

OPENAI_API_KEY="sk-..."
DATABASE_URL=postgresql://postgres:postgres@postgres:5432/invoices
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=password

Docker Compose (local)
----------------------
This repo includes a docker-compose.yml that starts:
- n8n (workflow engine)
- Postgres (database)

After populating `.env` run:

  docker-compose up --build

Access n8n at: http://localhost:5678

Importing Workflows into n8n
---------------------------
1. In n8n, go to the workflow import area and import the provided workflow JSON files (if any).
2. Update credentials inside n8n for OpenAI, SMTP, and any integrations.
3. Test with a sample invoice file.

OpenAI / GPT-4 Usage
--------------------
- Set your OpenAI API key in the environment: OPENAI_API_KEY
- Use short, deterministic prompts and include examples for better parsing of invoice fields.
- Log prompt + model response for auditing and debugging (PII caution).

Database
--------
- Run migrations (if present) or create required tables for invoices, audit logs, and approvals.
- The example docker-compose starts Postgres with default credentials from `.env.example`.

Common Tasks
------------
- To re-run an invoice through the flow, import its raw payload into the n8n trigger.
- To add a new validation rule, update the Validation node (or function) in n8n and add associated tests.

Troubleshooting
---------------
- If n8n doesn't start, check Docker logs and ensure ports are not used.
- If GPT calls fail, verify the OPENAI_API_KEY and the n8n credential setup.

