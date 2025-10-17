# Social Media Automation

Automates content creation and Facebook posting from a Google Sheet. For each row with a keyword and empty Status, the workflow:
- Generates a motivational quote with OpenAI
- Renders a branded image with the quote via Templated.io
- Writes the quote, caption, and image URL back to the sheet
- Posts the image + caption to a Facebook Page
- Marks the row as Posted

## How it works (flow)
1. Watch Google Sheets rows (Sheet1, “Motivational Quotes” spreadsheet, headers in A1:Z1; processes up to 5 at a time).
2. Filter: only process rows where Status (E) is blank.
3. Create quote (OpenAI, gpt-4o-mini) from Input_Keyword (A). Rules: 100–140 chars, no emojis/quotes/hashtags, simple and positive.
4. Write Motivational_Quote (B) to the sheet.
5. Render image (Templated.io) using a template; place the quote text in layer “Motivational_Quote”.
6. Write Image_Link (D) with the render URL.
7. Create Facebook caption (OpenAI) based on the quote; 1–2 relevant hashtags, no emojis, up to 4 lines, friendly tone.
8. Write Fb_Caption (C).
9. Download the rendered image from Image_Link.
10. Post to a Facebook Page with the image and caption.
11. Update Status (E) to “Posted” to prevent reprocessing.

Note: A secondary “Filter rows” step exists for rows with empty Status, but the main processing is already scoped to the current row via the initial filter.

## Inputs
- Google Sheet (Sheet1) with headers:
  - A: Input_Keyword (required)
  - B: Motivational_Quote (auto-filled)
  - C: Fb_Caption (auto-filled)
  - D: Image_Link (auto-filled)
  - E: Status (blank to process; becomes “Posted” after publishing)
- Connections:
  - Google Sheets (read/write)
  - OpenAI (model: gpt-4o-mini)
  - Templated.io (template ID configured; uses layer “Motivational_Quote”)
  - Facebook Pages (target Page ID configured)
- Optional: additional columns F–Z (ignored)

## Outputs
- Sheet updates per processed row:
  - B (Motivational_Quote): AI-generated quote
  - C (Fb_Caption): AI-generated caption
  - D (Image_Link): URL to the rendered image
  - E (Status): “Posted”
- Facebook post:
  - Photo: rendered quote image
  - Message: AI-generated caption

## Setup
1. Prepare the sheet with headers A–E as above (A1:Z1 contains headers).
2. Connect accounts: Google Sheets, OpenAI, Templated.io, Facebook Pages.
3. Set the Templated.io Template ID and ensure the text layer is named “Motivational_Quote”.
4. Set the Facebook Page ID to the page you want to post to.
5. Turn on the scenario. Add one or more keywords (A) with Status (E) left empty; the workflow fills B–D, posts, and sets E to “Posted”.

## Customization
- Adjust OpenAI prompt rules (length, tone, hashtags) in the two AI steps.
- Change the Templated.io template or layer names to match your design.
- Modify the posting destination by changing the Page ID.
- Increase the WatchRows limit if you want to process more rows per run.