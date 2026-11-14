# veritas-AI-hackathon-project
MedPlain: A Plain-Language Lab Report Explainer

Track: AI + Health
Team: Namit Battan, Aradhya Chauhan, Dakshesh Singh Pilkhwal

This project is a prototype and is not medical advice.

1. The Problem

Lab reports are full of abbreviations (SGPT, TLC, HbA1c), units and reference ranges. Patients often see them on paper or in a patient portal before they can talk to a doctor. A single number printed next to "H" or "L" can cause worry or be ignored.

They need a quick, calm explanation of what each result means and a list of useful questions to take to the doctor.

2. Submission Form Answers

Form Question| Answer
Project title| MedPlain: a plain-language lab report explainer
Building as| Team
Team members| Namit Battan, Aradhya Chauhan, Dakshesh Singh Pilkhwal
Team emails| namitbattancoc@gmail.com, aru000000018@gmail.com, dishu01101401251701@gmail.com
Track| AI + Health
Starting point| An original idea developed by our team after exploring the Health + AI theme provided in the project brief.
One-sentence pitch| MedPlain takes a photo or PDF of any lab report and explains in plain words which results are outside the range, what they can mean, and what to ask the doctor.
Write-up link| This document
Demo video link| Not included
What we added| The tool works on any lab report, not only blood tests. Unlisted tests are judged using the range printed on the report, and Positive/Negative results are handled too. We also fixed parsing bugs and created our own audit to compare the tool against hand-labelled reports.
Datasets, models and AI tools used| Tesseract OCR (via pytesseract), Pillow, poppler (pdftotext/pdftoppm), pandas, Python regular expressions, Kaggle datasets, publicly available laboratory reports, AI-related datasets and our own synthetic report images for the audit. AI tools used: Claude, Gemini and ChatGPT (GPT).
New to AI?| [Yes / No]

3. What We Built

MedPlain takes a laboratory report as a phone photo, scan or PDF and produces a plain-language summary.

It explains:

- How many results were successfully read.
- Which results are outside the reference range.
- What each test measures.
- Common reasons why a result may be low or high.
- Questions that patients can ask their doctors.

Example output from a synthetic test report:

🟠 Hemoglobin: 9.1 g/dL — LOW

Hemoglobin carries oxygen in your red blood cells. A low result may point to anaemia, which can have several possible causes, such as low iron, vitamin B12 or folate.

Range used: 12–15.5 (the range printed on the synthetic report).

The program runs as a single Google Colab notebook cell or locally using "python medplain.py report.pdf". Uploading a file displays the explanation and a table containing every result.

4. What We Added

The first version of MedPlain understood only 13 blood tests. We expanded it in four major ways:

1. Support for different laboratory reports

A built-in list of 47 common tests covers blood count, sugar, cholesterol, kidney function, electrolytes, liver function, thyroid, vitamins and iron.

Other tests can also be judged when the report provides its own reference range or an H/L flag. This allows urine, hormone and enzyme tests to work even when they are not included in the built-in list.

2. Qualitative results

Results such as Positive, Reactive, Detected or Trace are flagged when they are not the usual Negative, Non-reactive or Nil result. Blood group lines are intentionally skipped.

3. Photo and PDF support

Phone photographs are automatically rotated and resized. Digital PDFs use their existing text layer, while scanned PDFs are processed using OCR page by page.

4. Bug fixes

We identified and corrected several parsing and judging errors in the first version.

5. How It Works

Step| Process| Explanation
1. Read the text| Image processing and OCR| The image is straightened, converted to grayscale and contrast-boosted. Tesseract reads it using two page layouts, and the version that identifies more results is selected.
2. Find results| Text parsing| Python regular expressions identify the test name, value, unit, reference range and H/L flag. Numbers such as 11,2, 4,500 and 2,50,000 are handled. Unrelated lines such as "Page 1 of 2" or "Age 45" are ignored.
3. Judge each result| Range comparison| The report's own reference range is used first. If unavailable, a standard adult range is used only when the unit matches. If neither is available, the result is marked "couldn't judge" rather than guessed.
4. Explain| Plain-language output| The tool lists normal results, results outside the range, possible common reasons and useful questions to ask a doctor.

A value 30% or more beyond the edge of the reference range is marked red, and the output suggests contacting a doctor soon.

MedPlain uses a rule-based pipeline rather than a trained model. This means every decision can be traced to a line of text and a reference range.

6. Bugs We Found and Fixed

Problem in the First Version| Fix
Cholesterol printed as "Desirable less than 200, Borderline 200–239, High 240 or above" was incorrectly read as a normal range of 200–239.| Tiered ranges now use the normal/desirable tier.
Platelets in lakhs and WBC per cumm were compared using the wrong scale.| Values are rescaled to thousands per µL.
Standard ranges were applied even when units differed.| Standard ranges are used only when the unit matches.
"Mean corpuscular hemoglobin" matched Hemoglobin, and urine glucose matched blood glucose.| Longest-name matching and an exclusion list were introduced.
Numbers with decimal commas or thousands separators were misread.| A number reader that handles all three formats was introduced.
Missing files crashed the audit, and setup failures were silent.| File checks and clear error messages were added.

7. Results

Parsing Check (Completed)

We tested the parser and judging system on a 17-line synthetic report that we created ourselves.

The report included blood, thyroid, iron, liver, urine and infection results, along with header lines, a blood group and a decimal-comma value.

All 14 actual test results were successfully read and judged correctly. Header lines, page information and blood group lines were ignored.

This test checks the parsing and judging logic, not the OCR process.

OCR Audit

We plan to evaluate the OCR system using synthetic report images with manually labelled results.

Performance Comparison

Method| Abnormal Values Caught| Abnormal Values Missed| False Alarms| Values Not Read
Baseline: Label everything "normal"| 0| All abnormal values| 0| 0
MedPlain| Not yet measured| Not yet measured| Not yet measured| Not yet measured

The baseline is important because most values in a laboratory report are normal. Therefore, a system that labels everything as normal may appear accurate while missing every abnormal result.

The OCR performance still requires further testing to determine how frequently it misreads numbers and affects the final results.

8. Where It Fails

MedPlain has certain limitations:

- A blurry, shadowed or tilted photograph can make Tesseract misread digits. A misread number can change a result from normal to abnormal. The tool displays the numbers it reads so users can check them against the original report.
- Results printed across multiple lines or in complex tables may be missed.
- Standard ranges are generic adult ranges and are not adjusted for age, sex or pregnancy. The report's own reference range is preferred whenever available.
- Results written as "<0.5" are judged using the number 0.5, and urine microscopy ranges such as "2–3 /hpf" are not handled.
- MedPlain cannot diagnose diseases. A result outside the reference range does not automatically mean that a person has an illness. The possible reasons provided are general explanations, not diagnoses.

9. Safety and Privacy

Every output ends with a disclaimer. The tool also reminds users that results outside the reference range do not automatically indicate an illness.

Laboratory reports contain personal information. We tested MedPlain using synthetic reports only. A notebook running in Google Colab processes the uploaded file in that session, so real reports should only be uploaded by their owner.

MedPlain is designed to help people understand their reports and prepare questions for their doctors, not replace professional medical advice.

10. What We'd Do Next

In future versions, we would like to:

1. Run the audit on more reports and measure how often OCR changes a value.
2. Use age- and sex-specific reference ranges whenever the report provides them.
3. Improve support for multi-line tables and urine microscopy.
4. Display the user's original numbers alongside the extracted values so they can verify the reading.
5. Test MedPlain with users and understand whether the explanations and suggested questions help them prepare for a doctor's appointment.

11. Sources and References

Medical and Data Sources

- Kaggle medical and laboratory datasets.
- Publicly available laboratory reports.
- AI-related datasets.

Technical Sources

- Tesseract OCR and pytesseract.
- Pillow.
- Poppler-utils.
- Pandas.
- Python regular expressions.

AI Tools Used

AI Tool| Purpose
Claude| Used for debugging, coding assistance and resolving errors during development.
Gemini| Used for generating rough code, initial ideas and testing different approaches through trials.
ChatGPT (GPT)| Used for finalising the code, refining the project structure and improving the overall implementation and documentation.

---

Project: MedPlain — A Plain-Language Lab Report Explainer

Track: AI + Health

Team Members: Namit Battan, Aradhya Chauhan and Dakshesh Singh Pilkhwal

This project is a prototype and is not medical advice.