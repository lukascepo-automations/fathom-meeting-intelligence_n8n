# Fathom Meeting Intelligence

Automated meeting intelligence pipeline that processes **Fathom** video meeting transcripts into professional PDF summaries and AI-powered sales coaching reports, saved to **Microsoft SharePoint**.

## What It Does

- Monitors Outlook inbox for Fathom meeting recap emails
- Parses meeting metadata (title, participant, date, duration) from HTML
- AI generates structured meeting summary (key points, decisions, action items, next steps)
- Converts markdown to PDF via CloudConvert API
- Uploads PDF to SharePoint document library
- Triggers a separate Sales Coach AI that analyzes the meeting for sales performance
- Sales Coach scores calls across 5 dimensions (Discovery, Value Prop, Objection Handling, Next Steps, Relationship)
- Sales Coach uses company knowledge base (RAG) to suggest relevant case studies and talking points

## Architecture

```
--- Meeting Summary Pipeline ---

Outlook Trigger
    |
Filter Fathom Emails
    |
Parse Email (Code Node)
    |
AI Agent (GPT-4o-mini) -> Formats structured summary
    |
Format Output
    |               |
CloudConvert        Trigger Sales Coach
(md -> pdf)         (webhook)
    |
SharePoint Upload


--- Sales Coach Pipeline ---

Webhook (from main pipeline)
    |
Sales Coach Agent (GPT-5.2-pro)
    |                   |
    |           Company Knowledge Base
    |           (Supabase RAG)
    |
Format Output
    |
CloudConvert (md -> pdf)
    |
SharePoint Upload
```

## Workflows

| File | Description | Nodes |
|------|-------------|-------|
| `Fathom_Meeting_Summary_Pipeline.json` | Main pipeline -- email monitoring, AI summary, PDF generation, SharePoint upload, triggers Sales Coach | 16 |
| `Meeting_Intelligence_Sales_Coach.json` | Sales coaching AI -- analyzes meetings, scores performance, suggests improvements using company KB | 16 |

## Tech Stack

- **n8n** -- workflow automation
- **OpenAI GPT-4o-mini** -- meeting summary generation
- **OpenAI GPT-5.2-pro** -- sales coaching analysis
- **Microsoft Outlook** -- email trigger for Fathom recaps
- **Microsoft SharePoint** -- PDF document storage
- **CloudConvert API** -- markdown to PDF conversion
- **Supabase** -- vector store for sales knowledge base (case studies, pricing, playbook)

## Key Features

- **Fully automated end-to-end pipeline** -- email arrives, PDF appears on SharePoint with zero manual steps
- **AI meeting formatter** -- structured output with Key Points, Decisions, Action Items, Next Steps, and Q&A sections
- **Sales performance scoring** -- 5 dimensions rated 1-10 (Discovery, Value Proposition, Objection Handling, Next Steps, Relationship Building)
- **RAG-powered coaching** -- company knowledge base provides relevant case studies, pricing details, and playbook references
- **Markdown to PDF conversion** -- professional documents via CloudConvert
- **Built-in test data** -- included for development and testing
- **Error workflow integration** -- connected error handling for monitoring

## Required Credentials

| Credential | Purpose |
|------------|---------|
| OpenAI API | GPT-4o-mini (summaries) + GPT-5.2-pro (coaching) |
| Microsoft Outlook OAuth2 | Email monitoring for Fathom recaps |
| Microsoft SharePoint OAuth2 | PDF document upload |
| CloudConvert API (HTTP Header Auth) | Markdown to PDF conversion |
| Supabase API | Vector store for sales knowledge base |

## Setup

1. **Import workflows** -- import both `.json` files into your n8n instance
2. **Configure credentials** -- set up all credentials listed above in n8n
3. **Set up Outlook trigger** -- configure the email filter to match Fathom recap emails
4. **Configure SharePoint** -- set the target document library and folder paths for PDF uploads
5. **Set up CloudConvert** -- create an account and generate an API key for document conversion
6. **Prepare sales knowledge base** -- populate the Supabase vector store with company case studies, pricing, and sales playbook content
7. **Link workflows** -- update the webhook URL in the main pipeline to point to the Sales Coach workflow
8. **Activate workflows** -- enable both workflows and send a test Fathom email to verify the pipeline
