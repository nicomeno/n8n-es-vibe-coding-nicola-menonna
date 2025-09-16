# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This repository contains n8n workflow examples, specifically focused on email security analysis. The current directory contains a sophisticated email header analysis workflow for detecting IPs and spoofing attempts.

## Project Structure

- **Analyze_email_headers_for_IPs_and_spoofing__3.json**: Main n8n workflow file for email security analysis
- The workflow is part of a larger collection of n8n examples located in the parent directories

## n8n Workflow Architecture

### Main Workflow: Email Header Analysis for Security

The workflow (`Analyze_email_headers_for_IPs_and_spoofing__3.json`) is a comprehensive email security analysis system with the following architecture:

#### Input Processing
- **Webhook Trigger**: Receives email headers via POST request to path `90e9e395-1d40-4575-b2a0-fbf52c534167`
- **Header Extraction**: Processes raw email headers from webhook body

#### Two-Path Analysis System

**Path 1: IP Reputation Analysis**
- Extracts IP addresses from "received" headers using regex pattern matching
- Queries IP Quality Score API for fraud assessment (requires API key)
- Fetches geolocation data via IP-API service
- Evaluates fraud scores and assigns reputation categories:
  - Good: ≤10, OK: 11-49, Suspicious: 50-74, Poor: 75-84, Bad: ≥85
- Determines spam activity likelihood based on fraud scores

**Path 2: Email Authentication Analysis**
- Evaluates SPF, DKIM, and DMARC authentication results
- Checks authentication-results headers for pass/fail status
- Analyzes received-spf, dkim-signature, and received-dmarc headers
- Provides fallback authentication parsing when primary results are unavailable

#### Data Aggregation and Output Processing
- Merges IP analysis and authentication results
- Consolidates findings into structured JSON response
- **NEW**: Generates human-readable text reports with risk assessment
- **NEW**: Saves structured data to Google Sheets for tracking and analysis

### Key Technical Components

#### JavaScript Code Nodes
- Custom regex-based IP extraction from email headers
- Email header parsing and structure analysis
- Fraud score calculation and risk categorization
- Authentication status evaluation logic
- **NEW**: Text report generation with emoji formatting
- **NEW**: Google Sheets data preparation and transformation

#### External API Integrations
- **IP Quality Score API**: Fraud detection and abuse assessment (5000 credits/month free tier)
- **IP-API**: Geolocation and ISP information (45 requests/min rate limit)
- **NEW**: Google Sheets API for data persistence

#### Data Processing Flow
- Conditional routing based on header presence
- Item splitting for individual IP analysis
- Data merging and aggregation across analysis paths
- **NEW**: Multi-format output generation (JSON, text report, Google Sheets)

## Output Systems

### 1. Text Report (Primary Output)
Returns a formatted text report via webhook response with:
- **IP Reputation Analysis**: Detailed breakdown of each IP with fraud scores and reputation
- **Email Authentication Status**: Clear SPF/DKIM/DMARC validation results
- **Overall Security Assessment**: Risk level (LOW/MEDIUM/HIGH) with identified factors
- **Recommendations**: Action items based on risk assessment

### 2. Google Sheets Integration
Automatically saves analysis data to three separate sheets:

#### Sheet 1: Email_Analysis_Summary
- Analysis_ID, Timestamp, Risk_Level
- Total_IPs_Analyzed, High_Risk_IPs
- Authentication_Status (raw), Authentication_Status_Readable (human-friendly)
- Risk_Factors_Count, Processing_Status

**Authentication Status Transformations:**
- "Fully Authenticated" - All SPF/DKIM/DMARC passed
- "Partially Authenticated" - Some protocols passed, none failed
- "Partial Authentication" - Mix of pass/fail results
- "Authentication Failed" - Multiple protocols failed
- "Authentication Issues" - At least one protocol failed
- "Authentication Uncertain" - All protocols unknown
- "Authentication Unavailable" - No data available

#### Sheet 2: IP_Analysis_Details
- Analysis_ID, Timestamp, IP_Address
- Fraud_Score, Reputation, Organization, ISP
- Recent_Abuse, Spam_Activity, Tor_Exit_Node
- Country, Region

#### Sheet 3: Risk_Factors_Log
- Analysis_ID, Timestamp, Risk_Factor_ID
- Risk_Description, Risk_Level, Factor_Type
- Action_Required

## API Requirements

### IP Quality Score API
- Free tier: 5000 credits/month
- API key must be hardcoded in the workflow URL (n8n limitation)
- Replace `YOUR_API_KEY_HERE` in the "IP Quality Score" node
- Documentation: https://www.ipqualityscore.com/documentation/proxy-detection-api/overview

### IP-API
- Free tier: 45 requests/min
- No authentication required
- Rate limiting enforced with HTTP 429 responses
- Documentation: https://ip-api.com/docs

### Google Sheets API
- Requires Google Sheets API credentials configured in n8n
- Replace `YOUR_GOOGLE_SHEET_ID_HERE` in all three Google Sheets nodes
- Ensure three sheets exist: `Email_Analysis_Summary`, `IP_Analysis_Details`, `Risk_Factors_Log`

## Setup Instructions

1. **Configure API Keys**:
   - Update IP Quality Score API key in the "IP Quality Score" node URL
   - Set up Google Sheets authentication in n8n credentials

2. **Create Google Sheet**:
   - Create a new Google Sheet
   - Add three sheets with exact names: `Email_Analysis_Summary`, `IP_Analysis_Details`, `Risk_Factors_Log`
   - Replace `YOUR_GOOGLE_SHEET_ID_HERE` in all three Google Sheets nodes

3. **Test Webhook**:
   - Use the webhook URL: `[n8n-url]/webhook/90e9e395-1d40-4575-b2a0-fbf52c534167`
   - Send POST requests with email headers in the body

## Expected Input Format

The workflow expects email headers as plain text in the webhook body:

```
Delivered-To: user@domain.com
Received: by 2002:a05:7412:be08...
Authentication-Results: mx.google.com; spf=pass...
[additional headers]
```

## Output Format Examples

### Text Report Output
```
=== EMAIL SECURITY ANALYSIS REPORT ===

📍 IP REPUTATION ANALYSIS:
─────────────────────────────────
1. IP Address: 192.168.1.1
   Fraud Score: 87/100
   Reputation: Bad
   Organization: Example ISP
   ISP: Example ISP
   Recent Abuse: YES
   Spam Activity: Identified spam in the past 24-48 hours

🔐 EMAIL AUTHENTICATION STATUS:
─────────────────────────────────────
SPF (Sender Policy Framework): PASS
DKIM (DomainKeys Identified Mail): PASS
DMARC (Domain-based Message Authentication): FAIL

🛡️  OVERALL SECURITY ASSESSMENT:
─────────────────────────────────────
Risk Level: HIGH

Risk Factors Identified:
• High fraud score IP: 192.168.1.1
• DMARC authentication failed

🚨 This email shows high-risk indicators. Caution advised.
```

## Security Focus

This workflow is designed for **defensive security analysis only**:
- Email header authentication verification
- IP reputation assessment for threat detection
- Spoofing and phishing attempt identification
- Comprehensive logging and reporting for security teams
- No offensive security capabilities or credential harvesting

## Troubleshooting

### Common Issues
1. **IP Quality Score Error**: Ensure API key is correctly set in the node URL
2. **Google Sheets Error**: Verify credentials and sheet names match exactly
3. **Webhook Response Error**: Check that "respondWith" is set to "text" in "Respond to Webhook" node
4. **Missing Data**: Ensure all required sheets exist in Google Sheets with correct names