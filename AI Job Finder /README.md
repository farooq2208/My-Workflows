# Job Search (n8n) – Workflow README

## Purpose
Automate a daily job search, summarize each job, score fit against a predefined resume, log results to Google Sheets, generate an HTML email digest, send it via Gmail, and clean up the sheet afterward.

## High-level Flow
1. Schedule Trigger: runs daily at 07:00 (server timezone).
2. Set Job Title: sets a search term (default: “AI Engineer”).
3. Adzuna Search: fetches 5 US job postings via the Adzuna API.
4. Split Jobs: processes each job result individually.
5. Summarize Job (OpenAI): extracts company, location, short description.
6. Score Job (OpenAI): rates job-to-candidate fit (1–5) using a fixed resume.
7. Google Sheets (Append): stores each job as a row with key fields.
8. Google Sheets (Get rows): retrieves the latest rows.
9. Code (JS): combines rows into a single array for the email.
10. AI Agent (OpenAI): builds a full HTML email digest from the combined data.
11. Gmail: emails the digest to the configured recipient.
12. Google Sheets (Delete): removes recent rows to keep the sheet clean.

## Inputs & Configuration
- Schedule
  - Node: Schedule Trigger
  - Default: 07:00 daily
- Job Query
  - Node: Set Job Title
  - Field: job_title (default: “AI Engineer”)
- Adzuna API
  - Node: HTTP Request
  - Endpoint: /v1/api/jobs/us/search/1
  - Params: results_per_page=5, what={{job_title}}
  - Credentials: Adzuna app_id/app_key (store securely; do not hardcode in version control)
- OpenAI
  - Nodes: Summarize Job, Score Job, AI Agent, OpenAI Chat Model
  - Credentials: OpenAI API key
  - Models: gpt-4.1 / gpt-4.1-mini (adjust as needed)
- Candidate Profile
  - Node: Score Job
  - Note: Resume text is hardcoded in the prompt; edit to change the candidate profile.
- Google Sheets
  - Nodes: Google Sheets (Append, Get rows, Delete)
  - Config: Document ID, Sheet (Sheet1)
  - OAuth: Google Sheets OAuth2
- Email (Gmail)
  - Node: Gmail
  - sendTo: recipient email
  - subject: “Job Info”
  - OAuth: Gmail OAuth2
  - Note: The AI Agent outputs full HTML; ensure Gmail sends HTML content correctly.

## Outputs
- Google Sheets rows appended with columns:
  - Job Role, Job Description, Company Name, Location, Score, Apply At
- Email: one HTML “job digest” containing the 5 jobs, with inline-styled table and apply buttons.
- Cleanup: deletes up to 10 rows after sending to keep the sheet tidy.

## Notes & Customization
- Change the search term in “Set Job Title”.
- Adjust country/endpoint, results_per_page, and filters in the Adzuna request.
- Update the scoring rubric or resume in “Score Job”.
- Modify column mappings in the Google Sheets (Append) node.
- Update Gmail recipient/subject; add CC/BCC if needed.
- Tweak or remove the delete step if persistent logging is desired.
- Timezone: schedule runs in the n8n server’s timezone.

## Prerequisites
- Valid credentials: Adzuna, OpenAI, Google Sheets OAuth2, Gmail OAuth2.
- Sheet access granted to the connected Google account.

## Security
- Store API keys and OAuth credentials securely (n8n credentials store).
- Avoid committing secrets or personal data in repository history.