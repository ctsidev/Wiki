# Charlson Comorbidity Scores - UCLA CTSA Informatics Program

## Overview

The CTSA Informatics Program conducts weekly updates of a table containing Charlson Comorbidity Scores. The table, `LZ_CLARITY_CHARLSON_SCORE`, is kept in the I2B2 schema within the CLARITYQ database. This will be made available to OHIA for migration to SQL Server to enable wider use.

## Details

The table consists of 17 binary flags based on encounter diagnosis history. We do not currently use Problem List or Billing data. If a patient does not have any of the diagnoses present in the criteria, they will not have an entry in this table. The 17 flags are each assigned a weight that is used to determine the overall score as shown below.

In addition to the total score, we include an age adjusted score, which adds one to the total score for every decade of age past fifty. For example, a 65 year old patient would have two added to their total score.

## References

We use the enhanced ICD9 and ICD10 codes (Quan) to assign ICD codes to the measures. The links and table are pasted below:

- [Quan MedCare 2005](https://czresearch.com/dropbox/Quan_MedCare_2005v43p1130.pdf)
- Hude Quan, Bing Li, Chantal M. Couris, et al. "Updating and Validating the Charlson Comorbidity Index and Score for Risk Adjustment in Hospital Discharge Abstracts Using Data From 6 Countries," *American Journal of Epidemiology*, Volume 173, Issue 6, 15 March 2011, Pages 676–682, https://doi.org/10.1093/aje/kwq433

---

## TABLE 1. ICD-9-CM and ICD-10 Coding Algorithms for Charlson Comorbidities

| Comorbidities                  | Deyo's ICD-9-CM Codes                          | ICD-10 Codes                                         | Enhanced ICD-9-CM Codes                                  |
|-------------------------------|------------------------------------------------|-----------------------------------------------------|----------------------------------------------------------|
| Myocardial infarction          | 410.x, 412.x                                   | I21.x, I22.x, I25.2                                 | 410.x, 412.x                                             |
| Congestive heart failure       | 428.x                                          | I09.9, I11.0, I13.0, I13.2, I25.5, I42.0, I42.5-I42.9, I43.x, I50.x, P29.0 | 398.91, 402.01, 402.11, 402.91, 404.01, 404.03, 404.11, 404.13, 404.91, 404.93, 425.4-425.9, 428.x |
| Peripheral vascular disease    | 443.9, 441.x, 785.4, V43.4, Procedure 38.48   | I70.x, I71.x, I73.1, I73.8, I73.9, I77.1, I79.0, I79.2, K55.1, K55.8, K55.9, Z95.8, Z95.9 | 093.0, 437.3, 440.x, 441.x, 443.1-443.9, 447.1, 557.1, 557.9, V43.4 |
| Cerebrovascular disease        | 430.x-438.x                                    | G45.x, G46.x, H34.0, I60.x-I69.x                    | 362.34, 430.x-438.x                                     |
| Dementia                      | 290.x                                          | F00.x-F03.x, F05.1, G30.x, G31.1                    | 290.x, 294.1, 331.2                                     |
| Chronic pulmonary disease      | 490.x-505.x, 506.4                             | I27.8, I27.9, J40.x-J47.x, J60.x-J67.x, J68.4, J70.1, J70.3 | 416.8, 416.9, 490.x-505.x, 506.4, 508.1, 508.8          |
| Rheumatic disease             | 710.0, 710.1, 710.4, 714.0-714.2, 714.81, 725.x | M05.x, M06.x, M31.5, M32.x-M34.x, M35.1, M35.3, M36.0 | 446.5, 710.0-710.4, 714.0-714.2, 714.8, 725.x           |
| Peptic ulcer disease          | 531.x-534.x                                    | K25.x-K28.x                                         | 531.x-534.x                                             |
| Mild liver disease            | 571.2, 571.4-571.6                             | B18.x, K70.0-K70.3, K70.9, K71.3-K71.5, K71.7, K73.x, K74.x, K76.0, K76.2-K76.4, K76.8, K76.9, Z94.4 | 070.22, 070.23, 070.32, 070.33, 070.44, 070.54, 070.6, 070.9, 570.x, 571.x, 573.3, 573.4, 573.8, 573.9, V42.7 |
| Diabetes without chronic complication | 250.0-250.3, 250.7                          | E10.0, E10.1, E10.6, E10.8, E10.9, E11.0, E11.1, E11.6, E11.8, E11.9, E12.0, E12.1, E12.6, E12.8, E12.9, E13.0, E13.1, E13.6, E13.8, E13.9, E14.0, E14.1, E14.6, E14.8, E14.9 | 250.0-250.3, 250.8, 250.9                                  |
| Diabetes with chronic complication | 250.4-250.6                                  | E10.2-E10.5, E10.7, E11.2-E11.5, E11.7, E12.2-E12.5, E12.7, E13.2-E13.5, E13.7, E14.2-E14.5, E14.7 | 250.4-250.7                                             |
| Hemiplegia or paraplegia       | 344.1, 342.x                                   | G04.1, G11.4, G80.1, G80.2, G81.x, G82.x, G83.0-G83.4, G83.9 | 334.1, 342.x, 343.x, 344.0-344.6, 344.9                  |
| Renal disease                 | 582.x, 583-583.7, 585.x, 586.x, 588.x         | I12.0, I13.1, N03.2-N03.7, N05.2-N05.7, N18.x, N19.x, N25.0, Z49.0-Z49.2, Z94.0, Z99.2 | 403.01, 403.11, 403.91, 404.02, 404.03, 404.12, 404.13, 404.92, 404.93, 582.x, 583.0-583.7, 585.x, 586.x, 588.0, V42.0, V45.1, V56.x |
| Any malignancy, including lymphoma and leukemia, except malignant neoplasm of skin | 140.x-172.x, 174.x-195.8, 200.x-208.x | C00.x-C26.x, C30.x-C34.x, C37.x-C41.x, C43.x, C45.x-C58.x, C60.x-C76.x, C81.x-C85.x, C88.x, C90.x-C97.x | 140.x-172.x, 174.x-195.8, 200.x-208.x, 238.6               |
| Moderate or severe liver disease | 456.0-456.21, 572.2-572.8                     | I85.0, I85.9, I86.4, I98.2, K70.4, K71.1, K72.1, K72.9, K76.5, K76.6, K76.7 | 456.0-456.2, 572.2-572.8                                  |
| Metastatic solid tumor        | 196.x-199.1                                    | C77.x-C80.x                                         | 196.x-199.x                                             |
| AIDS/HIV                      | 042.x-044.x                                    | B20.x-B22.x, B24.x                                  | 042.x-044.x                                             |
