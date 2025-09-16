# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an n8n workflow project implementing an **Autonomous AI Crawler** that extracts social media profile links from company websites. The workflow uses AI agents with LangChain tools to autonomously navigate websites and retrieve structured data.

## Architecture

### Core Components

- **Main Workflow**: `Autonomous_AI_crawler.json` - Contains the complete n8n workflow JSON configuration
- **AI Agent System**: Uses OpenAI GPT-4o model with LangChain agents for autonomous web crawling
- **Data Pipeline**: Google Sheets integration for input data, Supabase for output data storage
- **Web Scraping Tools**: Custom n8n workflow tools for text and URL extraction

### Workflow Structure

1. **Data Input**: Retrieves company names and websites from Google Sheets `companies_input` sheet
2. **AI Crawling Agent**: Uses LangChain agent with two main tools:
   - `text_retrieval_tool`: Extracts all text content from webpages (converts HTML to Markdown)
   - `url_retrieval_tool`: Extracts and filters all URLs from webpages
3. **JSON Processing**: Structured output parser for social media profile data
4. **Data Output**: Stores extracted data in Supabase `companies_output` table

### Key Technologies

- **n8n**: Workflow automation platform
- **OpenAI GPT-4o**: AI model for intelligent web crawling
- **LangChain**: AI agent framework with tool integration
- **Google Sheets**: Input data source for company information
- **Supabase**: Database for output data storage
- **Custom HTTP requests**: Direct website content retrieval

## Data Flow

1. **Schedule trigger** starts the workflow automatically every 30 minutes
2. Fetch companies from Google Sheets `companies_input` sheet (name, website fields)
3. For each company:
   - AI agent crawls the website using available tools
   - Extracts social media URLs in structured JSON format
   - Merges company data with extracted social media profiles
   - Stores results in Supabase `companies_output` table

## Configuration Requirements

### Required Credentials
- **OpenAI API**: For GPT-4o model access
- **Google Sheets OAuth2**: For reading company data from spreadsheet
- **Supabase**: For output data storage

### Data Sources
- **Google Sheets** (`companies_input` sheet): Fields `name` and `website`
- **Supabase table** (`companies_output`): Includes company data plus `social_media` array

### AI Agent Configuration
- **Model**: GPT-4o with temperature 0 and JSON response format
- **System Message**: Configured for web crawling and social media extraction with specific JSON format requirements
- **Tools**: Text retrieval and URL retrieval workflow tools
- **Output Parser**: JSON schema for social media platform data with corrected required fields

## Development Notes

- The workflow includes proxy considerations for improved crawling accuracy
- HTML to Markdown conversion is used for better text processing
- URL validation and deduplication are implemented for reliable link extraction
- The AI agent can autonomously navigate through multiple pages to find social media links
- JSON schema validation ensures consistent output format for social media data
- Fixed JSON schema bug: corrected `required: ["platforms"]` to `required: ["social_media"]`
- Enhanced system message with specific JSON format examples to improve AI parsing accuracy

## Current Configuration

### Trigger System
- **Schedule Trigger**: Runs automatically every 30 minutes
- **Alternative**: Can be modified to webhook, manual, or other trigger types

### Data Sources Updated
- **Input**: Changed from Supabase to Google Sheets for better accessibility
- **Output**: Still uses Supabase for reliable data storage
- **Format**: Requires `name` and `website` columns in Google Sheets

### Troubleshooting Notes
- If `social_media` field returns empty arrays, check:
  1. Google Sheets has valid website URLs (with http/https)
  2. OpenAI API credentials are working
  3. AI agent system message includes proper JSON format instructions
  4. JSON parser schema matches expected output structure

## Workflow Modification

To adapt this workflow for different data extraction needs:
1. Modify the AI agent's system message and prompt with specific JSON format requirements
2. Update the JSON parser schema for different output formats (ensure required fields match)
3. Adjust the database table schemas as needed
4. Consider adding additional tools for specialized data extraction
5. Test with websites that definitely have social media links for validation