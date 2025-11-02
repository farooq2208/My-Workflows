# Student_Progress_Report_Latest

Automates student progress reporting from a Google Form to Google Sheets, generates an AI remark, creates a formatted report in Google Docs (and PDF), and emails it to the parent.

## What it does (high level)
1. Watches new Google Form responses (up to 3 per run).
2. Calculates:
   - Total obtained marks (sum of 9 subjects)
   - Percentage = Total / 555 × 100 (0 decimals, e.g., “83 %”)
   - Grade thresholds: A+ (≥80%), A (≥70), B (≥60), C (≥50), D (≥40), E (≥33), else F
3. Appends a row to a Google Sheet with student/profile fields, subject marks, totals, percentage, and grade.
4. Uses OpenAI (gpt-4.1-mini) to write a short, professional remark (≤200 chars) based on performance.
5. Updates the “Remarks” column in the same row.
6. Reads the sheet and creates a Google Doc report from a template for the most recent row only, then exports it as PDF.
7. Emails the PDF to the parent’s email.

## Trigger and services used
- Google Forms: Watch Responses
- Google Sheets: Add Row, Update Row, Filter Rows
- OpenAI: Create Chat Completion (remarks)
- Google Docs: Create from Template, Export as PDF
- Gmail: Send Email with attachment

## Required inputs
- Google Form with these fields (as plain text/numeric answers):
  - Roll No.
  - Student Name
  - Father’s Name (note: source label may read “Faher's Name”)
  - Contact No.
  - Class
  - Parent Email
  - Subject marks: Math, Phy, Chem, Bio/Comp, Eng, Urdu, Pak Std, Al-Quran, Islamiyat
- Connections:
  - Google (Forms/Sheets/Docs/Drive/Gmail)
  - OpenAI (gpt-4.1-mini)
- Resources/IDs (configure in the scenario):
  - Form ID
  - Spreadsheet and Sheet (“Students_Response” → “Sheet1”)
  - Google Docs template (placeholders must match keys listed below)
  - Destination folder for generated reports

## Sheet write mapping (headers)
- A–F: Roll No., Student Name, Father’s Name, Contact No., class, Parent Email
- G–O: Math, Phy, Chem, Bio/Comp, Eng, Urdu, Pak Std, Al-Quran, Islamiyat
- P–R: Total Obt. Marks, Percentage, Grade
- S: Remarks (AI-generated; filled after adding the row)

## Document template placeholders (required)
- Roll No., Student Name, Father’s Name, class, Contact No.
- Math, Phy, Chem, Bio/Comp, Eng, Urdu, Pak Std, Al-Quran, Islamiyat
- Total Obt. Marks, Percentage, Grade, Remarks

## Outputs
- A new row in Google Sheets with all details plus computed totals, percentage, and grade.
- “Remarks” column populated with a concise AI-generated comment.
- A Google Doc “Latest Progress Report” created and exported as PDF to Drive.
- An email sent to the Parent Email with the PDF attached.

## Notes and customization
- Max total is set to 555 for nine subjects; adjust the denominator if subjects/marks change.
- Percentage is formatted as text with a trailing space and % sign (e.g., “83 %”).
- Grade logic compares the formatted percentage text; if you change formatting, update the grade rules accordingly.
- The scenario creates the Doc/PDF only for the most recent row after reading the sheet.
- Ensure subject answers are numeric for correct summation.