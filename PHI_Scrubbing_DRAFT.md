# Data Cleaning Process to Remove PHI using JSL (OHIA)

This repository contains scripts and documentation for a **multi-step process** to clean data from Protected Health Information (PHI) to ensure compliance with privacy regulations.

---

## Table of Contents
- [Overview](#overview)
- [0 Naming convention for DataBricks upload folder](#0-naming-convention-for-databricks-upload-folder)
- [1 How to process a file](#1-how-to-process-a-file)
- [2 Validate Cleaned Data](#2-validate-cleaned-data)
- [3 Data Delivery](#3-data-delivery)
- [FAQ](#faq)



---
## 0 Naming convention for DataBricks upload folder:
- MednetUserName(ex Jsanz) the programmer working on the request
- PILastNamne_IRB#_FileType.csv (ex Elmnore_25-1895_Notes.csv)
> The options for FileType are: "Notes", "Imaging", "Pathology."


---
## 1 How to process a file
This [document](https://uclahs.app.box.com/file/1814822603942 "OHIA Step-by-step Document") from OHIA presents a step by step.



---
## 2 Validate Cleaned Data
- Use OHIA AI tool to analyze 10k records
- Manually review 375 cases of the  ones where no-PHI was found


---
## 3 Data Delivery 
- When dataset exceeds XXGB, open an ISS ticket for OHIA to copy the data from DataBrick de-identified object into the PI ULEAD folder
- Instructions pending*


---
## FAQ

- By default, we shall generate files by line/note [PENDING TO DECIDE]
- How to record the time in Jira/Monday?
- Time estimates based on volume
- Capture metadata
- Manage queue internally

Existing documetnation pending to reconcile
https://uclabip.atlassian.net/wiki/spaces/UBIG/pages/2343698434/PHI+Scrubber+Setup
https://uclabip.atlassian.net/wiki/spaces/UBIG/pages/2527854594/How+to+scrub+PHI+from+free+text+data+elements+DRAFT

---



- **Headers:** `#`, `##`, `###` for structure.
- **Bulleted lists:** `-` for steps and items.
- **Text boxes/notes:** Using blockquotes `>`.
- **Code blocks:** Triple backticks with language specified.
- **URL links:** `[text](url)` for clickable links.
- **Email link:** `mailto:` format inside `[text](mailto:email)`.
- **Horizontal rules:** `---` to separate sections.

Let me know if you want me to generate a customized README for your specific project!
