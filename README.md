# Resume Ranking Workflow (n8n Automation)

## Overview

This project is a **fully automated resume ranking system** built using **[n8n](https://n8n.io/)**.
It intelligently processes, analyzes, and ranks resumes for **AI/ML developer roles** by leveraging **Google Drive**, **Google Sheets**, and **AI automation**.

The workflow streamlines the recruitment process by:

1. Automatically fetching and processing resumes uploaded to Google Drive.
2. Extracting and cleaning text from PDF resumes.
3. Using a **Python-based scoring algorithm** to calculate skill relevance, education level, and experience.
4. Ranking resumes and updating results in a connected Google Sheet.
5. Sending a **daily AI-generated email** to HR with the **top 3 candidates**, including their key metrics and summaries.

---

## ⚙️ Key Features

### 🗂️ 1. Resume Upload Pipeline

**Trigger:** Manual execution
**Purpose:** Processes new resumes uploaded to Google Drive.

**Steps:**

* Lists all PDF files from a specified Google Drive folder.
* Downloads each resume and extracts its text content.
* Cleans the text by removing formatting, special characters, and unnecessary whitespace.
* Generates a **unique ID** for each resume.
* Appends the processed text into a **Google Sheet** for recordkeeping.

**Connected services:**

* Google Drive (to fetch resumes)
* Google Sheets (to store results)

---

### 🤖 2. Auto-Ranking Pipeline

**Trigger:** Every 1 minute (automated via scheduler)
**Purpose:** Continuously updates and ranks all resumes based on relevance.

**Steps:**

* Reads all rows from the Google Sheet.
* Runs a **Python-based algorithm** that:

  * Matches predefined **AI/ML keywords** (e.g., Python, TensorFlow, Machine Learning, NLP, PyTorch, etc.).
  * Calculates match percentage.
  * Extracts **experience years** from resume text using regex patterns.
  * Evaluates **education level** (PhD, Master’s, Bachelor’s, etc.).
  * Computes a **total score** based on weighted parameters.
* Assigns a **relevance label** (`Highly Relevant`, `Relevant`, `Somewhat Relevant`, or `Not Relevant`).
* Sorts resumes by total score and updates their **rank** in Google Sheets.

**Weights used:**

| Factor           | Weight |
| ---------------- | -----: |
| Keyword Match    |     10 |
| Match Percentage |      5 |
| Experience       |      3 |
| Education        |      2 |

**Output columns (in Google Sheet):**

* `# / ID`
* `Job Description`
* `Matched Keywords`
* `Missing Keywords`
* `Match Count`
* `Total Keywords`
* `Match %`
* `Experience Years`
* `Education Score`
* `Total Score`
* `Relevance`
* `Rank`

---

### 📧 3. Top Candidates Email Pipeline

**Trigger:** Daily at **8:00 AM** (automated)
**Purpose:** Automatically emails the HR team with a formatted summary of the top 3 candidates.

**Steps:**

* Fetches all ranked resumes from Google Sheets.
* Picks the top 3 candidates (based on `Rank` or `Total Score`).
* Generates short candidate summaries:

  * Name
  * Rank
  * Score
  * Match percentage
  * Matched keywords
  * 40-word resume snippet
* Sends this data to a **Groq-powered AI model** to create a **professional HTML email**.
* Cleans the AI output (removes markdown and reasoning tags).
* Sends the final email to HR via Gmail.

**Example Output:**

* Subject: `Top 3 Candidates • 10/11/2025`
* Body: HTML-formatted email with each candidate’s score, key skills, and a link to the full Google Sheet.

---

## 🧩 Technology Stack

| Component                         | Description                                        |
| --------------------------------- | -------------------------------------------------- |
| **n8n**                           | Core automation engine orchestrating all workflows |
| **Google Drive API**              | Source for uploaded resume PDFs                    |
| **Google Sheets API**             | Database for storing and updating candidate data   |
| **Python (within n8n Code Node)** | Implements custom ranking algorithm                |
| **Groq LLM**                      | AI model used to generate professional HTML emails |
| **Gmail API**                     | Sends the top candidate summary email to HR daily  |

---

## 🔄 Workflow Triggers Summary

| Workflow Name                     | Trigger        | Purpose                               |
| --------------------------------- | -------------- | ------------------------------------- |
| **Resume Upload Pipeline**        | Manual         | Process and store new resumes         |
| **Auto-Ranking Pipeline**         | Every 1 minute | Rank resumes and update Google Sheets |
| **Top Candidates Email Pipeline** | Daily at 8 AM  | Send top 3 candidates email summary   |

---

## 🧠 Algorithm Explanation

The ranking algorithm assigns each resume a **total score** using the following components:

```
Total Score = (MatchCount × 10) + (Match% × 5) + (ExperienceYears × 3) + (EducationScore × 2)
```

* **Keyword Matching:** Checks for presence of 30+ AI/ML-related keywords.
* **Experience Extraction:** Uses regex to find date ranges like `2018–2022` or `2020–present`.
* **Education Scoring:** Assigns numerical values based on degree level:

  * PhD → 10
  * Master’s → 8
  * Bachelor’s → 6
  * Diploma → 4
  * A-Levels → 3
  * Otherwise → 1
* **Relevance Classification:**

  * ≥70% → *Highly Relevant*
  * 50–69% → *Relevant*
  * 30–49% → *Somewhat Relevant*
  * <30% → *Not Relevant*

---

## 🧰 Setup Instructions

### 1. Prerequisites

* [n8n](https://n8n.io/) installed (self-hosted or cloud)
* Access to:

  * Google Drive
  * Google Sheets
  * Gmail account
  * Groq API key

### 2. Credentials

In n8n, create these credentials:

* **Google Drive OAuth2**
* **Google Sheets OAuth2**
* **Gmail OAuth2**
* **Groq API**

### 3. Import the Workflow

* Download the provided JSON file: `Resume Ranking (Working).json`
* Open n8n → click **Import Workflow** → upload the JSON.
* Update:

  * Google Drive folder ID for your resumes.
  * Google Sheets document ID.
  * Gmail recipient address (for HR).

### 4. Run

* Manually trigger the upload workflow once to process initial resumes.
* Ensure the schedule triggers are active for automatic updates and emails.

---

## 📬 Output Example

* **Google Sheet:** Continuously updated with ranked resumes and match data.
* **Daily Email:**
  Subject — `Top 3 Candidates • 10/11/2025`
  Body — AI-generated HTML summary for HR with:

  * Candidate names
  * Ranks & scores
  * Key skills matched
  * Resume snippet
  * Link to full sheet

---

## 🛠 Future Improvements

* Add job role detection for dynamic keyword sets.
* Integrate ATS (Applicant Tracking System) API for full recruitment automation.
* Expand email template customization via LLM prompts.



## 📄 License

**MIT License**


MIT License

Copyright (c) 2025 Muizz Khan

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

