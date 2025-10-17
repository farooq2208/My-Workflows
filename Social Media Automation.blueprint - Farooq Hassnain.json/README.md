# Social Media Automation

Automates creating and posting motivational quote content from a Google Sheet to a Facebook Page. For each row with a keyword and empty Status, the workflow generates a quote and caption with OpenAI, renders an image via Templated.io, posts it to Facebook, and writes results back to the sheet.

## How it works (high-level)
1. Watch up to 5 rows in a Google Sheet (Sheet1) each run.
2. Process only rows where Status (E) is empty.
3. Use OpenAI (gpt-4o-mini) to generate a motivational quote based on Input_Keyword (A).
4. Write the quote to Motivational_Quote (B).
5. Render an image using Templated.io (inserts the quote into a template) and store the render URL to Image_Link (D).
6. Use OpenAI to generate a short Facebook caption (1–2 lines + 1–2 hashtags) based on the quote.
7. Write the caption to Fb_Caption (C).
8. Download the rendered image and publish a photo post to the configured Facebook Page with the generated caption.
9. Mark Status (E) as Posted.

Note: There is an internal step that lists rows with empty Status, but the main posting path does not depend on it.

## Prerequisites
- Make.com account and the following connections:
  - Google Sheets
  - OpenAI
  - Templated.io
  - Facebook Pages (with publishing permissions for the target page)
- A Google Spreadsheet named or located at the path used in the scenario (Motivational Quotes), sheet name Sheet1.
- A Templated.io template with ID 15d3e43d-13fb-4700-9b1d-1d02ff6517db containing a text layer named Motivational_Quote.
- A Facebook Page ID you can post to (replace 781412975060094 with your own if needed).

## Sheet structure (inputs and outputs)
Use headers in row 1 as below:
- A: Input_Keyword (input)
- B: Motivational_Quote (auto-filled)
- C: Fb_Caption (auto-filled)
- D: Image_Link (auto-filled)
- E: Status (leave empty to be processed; set to Posted automatically after publishing)

## Inputs
- A Google Sheet row where:
  - Input_Keyword (A) is provided.
  - Status (E) is empty.
- Existing connections and template as noted above.

## Outputs
- Sheet updates per processed row:
  - Motivational_Quote (B): AI-generated quote (100–140 characters, no emojis/quotes/extra symbols).
  - Fb_Caption (C): AI-generated caption (1–2 lines, 1–2 relevant hashtags, no emojis, max 4 lines).
  - Image_Link (D): URL of the rendered image from Templated.io.
  - Status (E): Posted after successful Facebook publish.
- A new photo post on the configured Facebook Page with the generated caption and image.

## Configuration notes
- Processes up to 5 rows per run (adjust the limit in the Google Sheets “Watch Rows” module).
- The quote and caption use model gpt-4o-mini; you can switch models or adjust temperature/top_p if desired.
- The Templated.io layer name must match Motivational_Quote.
- Replace the Facebook page_id in the Facebook module with your target page.
- The scenario uses “User entered” mode when writing back to Google Sheets.

## Customization ideas
- Add scheduling in Make to run periodically.
- Extend prompts for brand voice or hashtag policies.
- Add error-handling branches (e.g., if render or post fails).
- Write post IDs or timestamps back to additional columns.