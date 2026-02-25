# THIS GUIDE IS NOT FINAL YET
# How to review a project:

The goal of this process is to produce high quality datasets to investigators by enforcing a quality check point in pipeline. 

The first step is to make sure that you are familiar with the specs that were shared through the documentation and the communication process thoughout the project life cycle. This is the information that you will need for your self-review, which is broken down into seven areas. 

> At the end of this process, you will post your review in Monday.com using the check list feature to make look like the example below. It's similar to the ones that you have received from your supervisor:
> <img width="407" height="324" alt="image" src="https://github.com/user-attachments/assets/68385399-ff69-4d64-bc36-bf10bc891483" />

You will check each area as completed, only if you have reviewed it, and didn't encounter any issues.

This guide provides a description of each area.

---
## Areas
- [1 Scripts](#1-Scripts)
- [2 Files](#2-Files)
- [3 Data dictionary](#3-Data-dictionary)
- [4 Hours](#4-Hours)
- [5 Cohort count](#5-Cohort-count)
- [6 Qualtrics changes](#6-Qualtrics-changes)
- [7 Admin](#7-Admin)


### 1 Scripts:
  * Confirmn that cohort selection is correct.
  * Read through the scripts to cobnfirm that they make logical sense and follow the correct pattern/order.
  * Checking staging tables counts as/when needed.
  * Verify that specific selection criteria (drivers) have been implemented when needed.

---
### 2 Files:
  * Make sure that the dataset matches the request. This is more important when ad hoc files (ex OBGYN) are part of it, or perhaps different data sources are involved (ex DICOM images).
  * Use size as an indicator of quality (ex an oversized file can point to a faulty join that results in redundant or innacurate records).
  * Always confirm that PHI is ONLY included when allowed.

---
### 3 Data dictionary:
  * Confirm that it matches the dataset.
  * It's important that the cohort definition in the heading section is accurate (ex this could have changed during the cohort discovery process and needed to be udpated).
  * Ad hoc items shall also be part of it.

---
### 4 Hours:
  * Confirm that you have logged all your effort on this project.
  * In the event when the number of houurs is unusually high, please add a comment describing why.

---
### 5 Cohort count
  * Confirm that are only providing as many records as we are allowed.

---
### 6 Qualtrics changes: 
  * Any chagnes that might have happened during the project discussion shall be captured in the final documentation, so any future review would be looking at accurate records (ex: DUA patient count increase, Qualtrics data elements section up to date...).

---
### 7 Admin
  * The Clarity [i2b2.BIP_project] and [i2b2.BIP_entity] tables shall incklude the proper records.
  * HIPAA disclosure information ready to share with the Consultant.


If during this process you identify anything that needs a redo, or perhaps input from your director or the consultant, raise the appropriate question in the ticket to solve it ASAP. If the issue has to do with your work, address it until you can produce a clean review.

---
Once the seven areas of the review have been cleared by you:
- you must publish your review in Monday.com
- Tag your director
- add the "Programmer review" date to set the project to receive approval for delivery.

