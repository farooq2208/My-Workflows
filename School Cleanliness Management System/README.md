# School Cleanliness Management System

A Make (Integromat) scenario that turns Google Form submissions into targeted cleanliness alerts. It converts a selected list of school areas into JSON, routes each area to the responsible staff member, looks up their name from Google Sheets, and emails an alert.

## What it does (flow)
1. Trigger: Watches new Google Form responses (Form ID: 1R9nCLSKOboRu4wptd2wl6dC5vM1yTnSDYB7JO4tCxuQ).
2. Transform: Uses OpenAI to convert a Google Forms answer array (question ID: 4761982f) into a clean JSON list of areas.
3. Parse: Parses the JSON into a list of areas.
4. Route: For each selected area, routes to the responsible person’s branch.
5. Lookup: Fetches the responsible person’s name from Google Sheets:
   - Spreadsheet ID: 1bYgq3JIqILqWG6ToxpMC_ZbmXBJzTOQaW1X2LDWyazo
   - Sheet: Sheet1
   - Columns: A = Area, B = Responsible Person
6. Notify: Sends a “Cleanliness Alert” email via Gmail to the configured recipient (currently: pmaf16e018@outlook.com), greeting the responsible person by name and listing the area(s).

## Inputs
- Google Forms
  - Form ID: 1R9nCLSKOboRu4wptd2wl6dC5vM1yTnSDYB7JO4tCxuQ
  - Question ID to read: 4761982f (expected to contain the selected areas as an array)
- Google Sheets
  - Spreadsheet ID: 1bYgq3JIqILqWG6ToxpMC_ZbmXBJzTOQaW1X2LDWyazo
  - Sheet: Sheet1
  - Mapping: Column A = Area, Column B = Responsible Person (rows must match exact area names)
- Connections (Make)
  - Google (Forms, Sheets)
  - Gmail
  - OpenAI
- Email recipient
  - Default: pmaf16e018@outlook.com (can be changed)

## Area-to-person routing
- Rana Jameel: Bio Lab, Computer Lab, Office, Gallery, Staff Room, Lawn 1, Lawn 1 Front
- Ghulam Raza: Lawn 2, Assembly Ground, Backside, Room 1, Room 2, Hall, Hall Ground, Office Backside
- Zulqarnain: Lawn 3, Masjid, Gate Front
- Azhar Hussain: Upper Story, Corridor, Lawn 4, Washrooms
- Abdul Ghaffar: Canteen Front, Canteen Lawn

## Outputs
- One or more emails titled “Cleanliness Alert” sent via Gmail to the configured recipient. Body format:
  - Dear [Responsible Person Name]!
  - Please ensure cleanliness at the following places: [comma-separated list of selected areas]
  - Regards
    Sheikh M. Farooq Hassnain

## Notes and assumptions
- The scenario expects the Google Forms answer for question ID 4761982f to be an array of area names that exactly match the names in the Google Sheet (Column A).
- The OpenAI step is used only to normalize the array into JSON text; ensure your OpenAI connection is active.
- If a selected area does not match any routing branch, no email is sent for that area.
- The Gmail module currently sends all alerts to a single email address; adjust “To” (and optionally CC/BCC) as needed.

## How to customize
- Change recipient: Update the “To” field in the Gmail modules.
- Change subject/body: Edit the Gmail module’s Subject and content.
- Add/modify areas and responsible persons:
  - Update the Google Sheet (Area in Column A, Responsible Person in Column B).
  - Add or adjust router filters in Make if introducing new staff or area groups.
- Replace the OpenAI step (optional): If your form already outputs structured JSON, you can remove the OpenAI conversion and map directly to the JSON parser or skip parsing entirely.

## Troubleshooting
- No email sent: Confirm the selected area exactly matches a router condition and exists in the Sheet.
- Wrong or missing name: Check the Sheet row for the area; Column B must have the correct person’s name.
- Parsing issues: Ensure the OpenAI step returns valid JSON and the JSON Parse module is receiving the response content.