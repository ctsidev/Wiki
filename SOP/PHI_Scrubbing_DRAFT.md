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
- Language to share with investigator(s)
- 
You are receiving this dataset of clinical notes under the regulatory framework governing patient privacy and data protection, including the Health Insurance Portability and Accountability Act (HIPAA). This legislation requires that all shared health data be de-identified to prevent disclosure of Protected Health Information (PHI).

    Regulatory Enforcement of De-identification
    In compliance with HIPAA and related laws, this dataset has been de-identified to remove or obscure any information that could directly or indirectly identify patients, ensuring confidentiality and lawful data sharing.

    ### Overview of the De-identification Process
    An advanced PHI scrubbing tool from John Snow Labs, using AI, was applied to identify and remove PHI from free-text clinical notes, including names, dates, locations, and other identifiers. While robust, some residual PHI may remain due to the complexity of clinical text.

  Our deidentification pipeline prioritizes eliminating false negatives (PHI leakage) over reducing false positives (non-PHI incorrectly annotated as PHI), while operating in a scalable and efficient manner. This can produce an over-redacting of certain text elements. We are workign diligently to improce these situations while still providing the maximum level of protection.

    #### Protocol if Potential PHI is Found
    If you discover potential PHI or sensitive identifiers in the dataset, please:

    - Immediately stop further use or sharing to prevent disclosure.
    - Notify the honest broker team who provisioned the data with details about the suspected PHI
    - Securely provide specific examples or excerpts of the suspected PHI to assist investigation.
    - Await further instructions before resuming use or distribution.
    - Notify the UCLA compliance team at compoffice@mednet.ucla.edu.

Your prompt cooperation is essential to maintain privacy compliance and protect patient information.

---
## FAQ

- By default, we shall generate files by line/note [PENDING TO DECIDE]
- How to record the time in Jira/Monday?
- Time estimates based on volume
- Capture metadata
- how long are records to be kept in DataBricks? Is it completely up to OHIA, or are there files/objects that we can delete?
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
