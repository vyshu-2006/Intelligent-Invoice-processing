# Intelligent Invoice Processing

An intelligent, AI-powered invoice processing automation system built with n8n that combines GPT-4 extraction, validation, and smart approval workflows to streamline end-to-end invoice handling.

[![Repository size](https://img.shields.io/github/repo-size/vyshu-2006/Intelligent-Invoice-processing)](https://github.com/vyshu-2006/Intelligent-Invoice-processing)
[![License](https://img.shields.io/github/license/vyshu-2006/Intelligent-Invoice-processing)](LICENSE)
[![Open Issues](https://img.shields.io/github/issues-raw/vyshu-2006/Intelligent-Invoice-processing)](https://github.com/vyshu-2006/Intelligent-Invoice-processing/issues)


Table of Contents
- About
- Key Features
- Tech Stack
- Architecture
- Quick Start
- Running Locally (Docker)
- n8n & Workflow Notes
- Contributing
- Security
- License

About
------
This project automates invoice intake, data extraction, validation, exception handling and approvals using n8n workflows orchestrating GPT-4-based extraction and a set of validation & approval rules. It's designed to reduce manual effort, improve accuracy, and accelerate invoice processing.

Key Features
------------
- GPT-4-powered OCR/text extraction and semantic understanding for invoice fields (vendor, invoice number, date, amounts, line items)
- Configurable validation rules & schema enforcement
- Smart approval flows with human-in-the-loop handling for exceptions
- Auditable processing history and validation traceability
- Extensible n8n-based workflow orchestration

Tech stack
----------
- n8n (workflow automation)
- OpenAI GPT-4 for extraction and validation prompts
- Postgres (recommended) or other persistent storage
- Redis / Message queue (optional) for background jobs
- Node.js scripts (helpers/transformers) when needed

Architecture
------------
See ARCHITECTURE.md for an overview and diagrams.

Quick Start
-----------
1. Clone the repo:

   git clone https://github.com/vyshu-2006/Intelligent-Invoice-processing.git
   cd Intelligent-Invoice-processing

2. Copy .env.example to .env and fill in secrets (OpenAI API key, DB URL, etc.)

3. Start development environment using Docker Compose (recommended):

   docker-compose up --build

4. Open n8n at http://localhost:5678 and import the workflows (see SETUP.md)

Running Locally (Docker)
------------------------
This repo includes an example docker-compose.yml for local development which will start n8n and a Postgres database. See SETUP.md for environment variables and instructions.

n8n & Workflow Notes
--------------------
- Workflows are stored as n8n JSON exports (import into n8n UI).
- The core flow:
  1. Ingest invoice (email/URL/upload)
  2. Pre-process (image clean-up / OCR)
  3. GPT-4 extraction prompt -> structured JSON
  4. Validation rules engine -> pass / exceptions
  5. Exceptions routed to human approval flows
  6. Save approved invoice to DB / downstream systems

Contributing
------------
See CONTRIBUTING.md for guidelines.

Security
--------
If you discover a security vulnerability, please report it according to SECURITY.md.

License
-------
This project is licensed under the MIT License — see the LICENSE file for details.
