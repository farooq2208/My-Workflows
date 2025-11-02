# Student_Progress_Report_Latest

Automates end-to-end creation and emailing of a student progress report from a Google Form submission. It logs results to Google Sheets, computes totals, percentage, and grade, generates concise AI remarks, produces a Google Doc (and PDF), and emails the PDF to the parent.

## How it works (Flow)
1. Trigger: Google Forms — on new responses (watchResponses).
2. Compute:
   - Total Obtained Marks = sum of subject marks (Math, Phy, Chem, Bio/Comp, Eng, Urdu, Pak Std, Al-Quran, Islamiyat).
   - Percentage = (Total / 555) × 100, rounded to 0 decimals, with a % suffix.
   - Grade (thresholds below).
3. Google Sheets — append a new row with all fields and computed values.
4. OpenAI — generate a 1–1.5 line professional remark based on totals/percentage/grade.
5. Google Sheets — update the “Remarks (S)” cell in the same row.
6. Google Sheets — read and sort rows; select the last (newest) row.
7. Google Docs — create a Doc from a template populated with student data.
8. Google Docs — export the new Doc to PDF.
9. Gmail — email the PDF to the parent email.

The “FilterRows” step is used with a “Cut off” condition so document generation and emailing run once for the latest appended row.

## Inputs
- From Google Form (expected fields):
  - Roll No.
  - Student Name
  - Father’s Name
  - Contact No.
  - Class
  - Parent Email
  - Subject marks: Math, Phy, Chem, Bio/Comp, Eng, Urdu, Pak Std, Al-Quran, Islamiyat
- Connections (Make.com):
  - Google (Forms, Sheets, Docs, Drive)
  - Gmail
  - OpenAI

## Outputs
- Google Sheets (Students_Response > Sheet1): a new row with:
  - A: Roll No.
  - B: Student Name
  - C: Father’s Name
  - D: Contact No.
  - E: Class
  - F: Parent Email
  - G: Math
  - H: Phy
  - I: Chem
  - J: Bio/Comp
  - K: Eng
  - L: Urdu
  - M: Pak Std
  - N: Al-Quran
  - O: Islamiyat
  - P: Total Obt. Marks
  - Q: Percentage
  - R: Grade
  - S: Remarks (AI-generated)
- Google Doc: “Latest Progress Report” created from a template and saved to Drive/folder.
- PDF: exported from the Doc and stored in Drive.
- Email: sent to Parent Email with the PDF attached.

## Grading & Calculations
- Total: sum of 9 subjects.
- Percentage: out of 555 total marks, 0 decimal places, e.g., “85 %”.
- Grade thresholds:
  - A+ ≥ 80%
  - A ≥ 70%
  - B ≥ 60%
  - C ≥ 50%
  - D ≥ 40%
  - E ≥ 33%
  - F < 33%

## AI Remarks
- Model: gpt-4.1-mini
- Output: 1–1.5 lines (≤200 chars), professional tone.
- Logic: Encouraging for ≥80%, motivating for 60–79%, balanced for 40–59%, encouraging but firm <40%.
- No marks/percentages/subjects cited; no emojis or extra symbols.

## Google Doc Template Placeholders
The template must contain placeholders matching these keys:
- Roll No., Student Name, Father’s Name, class, Contact No.
- Math, Phy, Chem, Bio/Comp, Eng, Urdu, Pak Std, Al-Quran, Islamiyat
- Total Obt. Marks, Percentage, Grade, Remarks

## Configuration you’ll need to set
- Google Form: formId in the Forms “watchResponses” module.
- Google Sheet: spreadsheetId and sheetId (Sheet1) in “addRow”, “updateRow”, and “filterRows”.
- Google Docs:
  - Template document ID in “createADocumentFromTemplate”.
  - Destination folder ID for the generated reports.
- Connections: authenticate Google, Gmail, and OpenAI accounts in Make.com.

## Notes and assumptions
- Subject marks must be numeric in the Form.
- Percentage is formatted with a trailing % sign; ensure grade comparisons handle numeric evaluation in your environment if you change formatting.
- Total marks denominator (555) is hardcoded; adjust for your exam scheme.
- The “watchResponses” module fetches up to 3 per run (limit=3); adjust if needed.
- “FilterRows” selects the last row to drive Doc/PDF/Email for the newest entry. Ensure your sheet’s sort order reflects “latest row last.”

## Maintenance tips
- Update mapping if you rename Form questions.
- If you change sheet columns, update the column-to-field mappings in “addRow” and “updateRow”.
- If you change the template text/placeholders, keep the keys synchronized with the “requests” mapping in the Docs module.