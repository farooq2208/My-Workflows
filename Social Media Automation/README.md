# Social Media Automation

Automates turning a keyword in Google Sheets into:
- a motivational quote (OpenAI),
- a designed image (Templated.io),
- a Facebook photo post (Facebook Pages),
- and updates the sheet with outputs and status.

Only rows with a blank Status are processed.

## What it does (flow)
1. Watch rows in Google Sheets (Sheet1) and take up to 5 rows per run.
2. If Status (column E) is blank:
   - Generate a motivational quote from the Input_Keyword (OpenAI, gpt-4o-mini).
   - Write the quote back to column B.
   - Render a quote image with Templated.io using a template layer named “Motivational_Quote”.
   - Save the image URL to column D.
   - Generate a Facebook caption (without repeating the quote) from the quote text (OpenAI).
   - Save the caption to column C.
   - Download the rendered image.
   - Create a Facebook photo post on the configured Page using the image and caption.
   - Mark Status (column E) as “Posted”.

Note: The scenario includes a “Filter Rows” step that’s not used downstream; you can remove it safely.

## Inputs
- Google Sheet (Sheet1) with these headers:
  - A: Input_Keyword (required; trigger for content creation)
  - B: Motivational_Quote (auto-filled)
  - C: Fb_Caption (auto-filled)
  - D: Image_Link (auto-filled)
  - E: Status (leave blank to queue; set to “Posted” after completion)

- Connections and IDs to configure:
  - Google Sheets connection and Spreadsheet ID
  - OpenAI connection (model: gpt-4o-mini)
  - Templated.io connection and Template ID
    - Template must include a text layer named “Motivational_Quote”
  - Facebook Pages connection and Page ID

## Outputs
- Updates the same Google Sheet row:
  - B: Generated quote (100–140 chars, no emojis/hashtags/quotes)
  - C: Facebook caption (1–4 lines, 1–2 hashtags, no emojis or question marks)
  - D: Public image URL rendered via Templated.io
  - E: Status = “Posted”
- A new Facebook photo post on the configured Page with the rendered image and caption.

## Content rules (built into prompts)
- Quote: 100–140 characters; clear, simple, motivational; no emojis/hashtags/quotes/symbols.
- Caption: Expand meaning in 1–2 lines; add 1–2 relevant hashtags; no emojis or question marks; ≤4 lines; friendly, cheerful tone.

## Setup
1. Prepare the Google Sheet with headers A–E exactly as listed above.
2. Add your OpenAI, Google, Templated.io, and Facebook connections in Make.
3. Set:
   - Spreadsheet ID and Sheet name (Sheet1)
   - Templated.io Template ID (ensure layer “Motivational_Quote” exists)
   - Facebook Page ID
4. Turn on the scenario and schedule as needed.

## Notes
- The scenario processes up to 5 rows per run (configurable in the Watch Rows step).
- It only processes rows where Status (E) is blank—use this to control posting.
- All URLs used for image rendering/posting should be publicly accessible.