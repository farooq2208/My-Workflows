# Job Search (n8n Workflow)

## Purpose
Automate a daily job search, summarize each job, score it against a candidate resume, log results to Google Sheets, generate a full HTML email digest, send it via Gmail, and clean up the sheet for the next run.

## How it works (high level)
- Triggers daily at 07:00.
- Searches Adzuna (US) for the configured job title (default: “AI Engineer”), returning 5 results.
- Splits results and uses OpenAI to:
  - Extract key fields (company, location, description).
  - Score each job 1–5 against a preset resume.
- Appends the results to Google Sheets.
- Combines the 5 rows and uses an AI Agent to render a full-width HTML email digest.
- Sends the digest via Gmail.
- Deletes up to 10 rows in the sheet to reset for the next run.

## Key nodes and data flow
1. Schedule Trigger → runs at 07:00.
2. Set Job Title → sets job_title (default: AI Engineer).
3. HTTP Request → fetches top 5 US jobs from Adzuna for job_title.
4. SplitOut → one item per job.
5. OpenAI (Summarize Job) → extracts company_name, location, description.
6. OpenAI (Score Job) → returns {"score": "..."} for the candidate match.
7. Google Sheets (Append) → writes columns:
   - Job Role, Job Description, Company Name, Location, Score, Apply At
8. Google Sheets (Get rows) → loads all rows.
9. Code (JS) → bundles rows into sheetData array.
10. OpenAI Chat Model + AI Agent → builds a complete HTML email digest.
11. Gmail → sends the HTML email.
12. Google Sheets (Delete) → deletes 10 rows as cleanup.

Note: The workflow is currently inactive (active: false).

## Inputs you configure
- Job title: Set in “Set Job Title” node.
- Schedule time: In “Schedule Trigger.”
- Adzuna API:
  - Country: us (endpoint path)
  - results_per_page: 5 (URL param)
  - app_id and app_key: replace with your own; store securely (e.g., env vars).
- Candidate resume: Edit the “Score Job” node message content.
- Google Sheet: documentId and sheetName.
- Email recipient: “Gmail” node sendTo address.

## Credentials required
- Adzuna app_id and app_key.
- OpenAI API key (for GPT-4.1 / GPT-4.1-mini).
- Google Sheets OAuth2.
- Gmail OAuth2.

## Outputs
- Google Sheets row per job with:
  - Job Role, Job Description, Company Name, Location, Score, Apply At
- HTML email sent via Gmail:
  - Full-width, inline-styled digest
  - Columns: Job Role, Company Name, Location, Score (highlighted if high), full Job Description, Apply button
- Cleanup: Up to 10 rows deleted from the sheet post-send.

## Setup (quick start)
1. Add and test credentials for OpenAI, Google Sheets, and Gmail.
2. Replace Adzuna app_id/app_key in the HTTP Request URL (prefer env vars/credentials).
3. Create a Google Sheet with headers: Job Role, Job Description, Company Name, Location, Score, Apply At.
4. Update:
   - Job title (Set node)
   - Email recipient (Gmail node)
   - Resume text (Score Job node)
5. Run once manually to verify, then activate the workflow.

## Notes and caveats
- The email agent prompt forces full job descriptions; emails may be long.
- The sheet delete step may remove headers; adjust numberToDelete or protect the header row.
- Minor template issues:
  - “Compnay” misspelled in the email template.
  - Job 2 “Apply At” references the first job’s link; update to the correct index.
- Consider adding location/filters to the Adzuna query if needed.