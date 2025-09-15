# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This repository contains an n8n workflow for AI-powered email processing with autoresponder functionality and manual approval. The workflow is designed to:

1. Monitor incoming emails via IMAP trigger
2. Convert email content to markdown format for better LLM processing
3. Summarize email content using AI summarization chains
4. Generate appropriate responses using RAG (Retrieval Augmented Generation) with Qdrant vector database
5. Send draft responses to Gmail for manual approval (Yes/No)
6. Send approved responses via SMTP

## Architecture

### Core Components

- **Email Trigger (IMAP)**: Monitors incoming emails from configured email accounts
- **Markdown Conversion**: Converts HTML email content to markdown for better LLM understanding
- **Email Summarization Chain**: Uses DeepSeek R1 model to create concise summaries (max 100 words)
- **RAG System**:
  - Qdrant Vector Store for company knowledge base retrieval
  - OpenAI Embeddings for vectorization
  - Agent that combines retrieved knowledge with email context
- **Approval Flow**: Gmail integration with "Send and Wait" functionality for manual approval
- **Response Generation**: OpenAI GPT-4o-mini for final email composition

### Workflow Flow

1. Email received → Markdown conversion → Summarization → RAG-enhanced response generation → Draft approval → Send response

### Key Configuration Requirements

- **IMAP Credentials**: Required for email monitoring
- **SMTP Credentials**: Required for sending responses
- **OpenAI API**: Used for embeddings and response generation
- **DeepSeek API**: Used for email summarization
- **Qdrant Database**: Vector database must be pre-populated with company knowledge
- **Gmail OAuth2**: Required for approval workflow (must use Gmail address for approval functionality)

## Important Notes

- The approval system specifically requires Gmail because it's the only service supporting the "Send and Wait" functionality
- Vector database on Qdrant must be pre-configured and populated with relevant company documents
- All responses are limited to 100 words and formatted in HTML
- The workflow includes comprehensive error handling and conditional logic for approval flows

## Files Structure

- `AI-powered-email-processing-autoresponder-approval.json`: Main n8n workflow file ready for import
- `AI-powered email processing autoresponder and response approval (Yes_No).txt`: Original workflow data (maintained for reference)
- `CLAUDE.md`: This documentation file

## Development Context

This is an n8n workflow configuration file, not traditional source code. Changes should be made through the n8n interface or by carefully editing the JSON structure while maintaining node relationships and data flow integrity.

## Import Instructions

To import this workflow into n8n:
1. Open your n8n instance
2. Go to Workflows section
3. Click "Import from file"
4. Select `AI-powered-email-processing-autoresponder-approval.json`
5. Configure the required credentials before activation