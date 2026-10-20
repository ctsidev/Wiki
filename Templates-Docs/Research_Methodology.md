Research reported in this publication was supported by the Biomedical Informatics Program (BIP) at UCLA CTSI and funded by the National Institutes of Health (NIH) through the National Center for Advancing Translational Sciences (NCATS) Grant (#UL1TR001881)

## Methodology Language for Publishing

Clinical data for this retrospective study were extracted from institutional electronic health record (EHR) repositories and affiliated research databases under approved data governance and privacy protocols. Investigators and informatics specialists collaborated to define cohort criteria, inclusion and exclusion parameters, and required variables. Data elements were identified using institutional data dictionaries and standardized data models to ensure consistent definitions.

Structured queries were developed to extract relevant data domains, including demographics, diagnoses, procedures, medications, and laboratory results. Iterative refinement of cohort definitions was conducted as needed. Prior to release, datasets underwent validation checks for completeness, plausibility, and consistency.

Datasets were de-identified or limited in accordance with approved protocols and delivered via secure, access-controlled research environments in standard analytic formats. Post-extraction clarification was available to ensure accurate interpretation of variables and data structure.

## Methodology – Technical

Data were sourced from enterprise clinical data repositories aggregating EHR data and related clinical systems. Extraction activities were conducted under institutional review and data governance approval for secondary use of health data. A requirements elicitation process involving the Principal Investigator and informatics personnel defined computable phenotypes, cohort logic, temporal constraints, and variable specifications.

Data elements were mapped to institutional data dictionaries and common data models to support semantic harmonization and reproducibility. Standard clinical terminologies (e.g., diagnosis, procedure, laboratory, and medication vocabularies) were used where available. Non-standard elements were operationalized through documented custom mappings.

Cohort identification employed SQL-based queries and, where necessary, iterative cohort discovery workflows to refine inclusion and exclusion logic. Extraction pipelines incorporated data quality assessments, including conformance, completeness, and plausibility checks. Record counts and summary statistics were compared against source system aggregates when feasible.

Privacy-preserving measures were implemented consistent with regulatory and institutional requirements. Depending on protocol specifications, datasets were de-identified or configured as limited datasets. Additional PHI scrubbing procedures were applied when warranted.

Final datasets were provisioned within secure, compliance-approved research environments using structured analytic formats. Access was restricted to authorized personnel. Metadata and data provenance documentation accompanied each dataset to support analytic transparency and reproducibility.

## Condensed Version

Clinical data were retrospectively extracted from institutional electronic health record repositories under approved governance and privacy protocols. Investigators and informatics specialists defined computable cohort criteria and variables using standardized data dictionaries and common data models. Structured queries were used to obtain relevant clinical domains, and datasets underwent validation for completeness and plausibility. Data were de-identified or limited per protocol and delivered within secure, access-controlled research environments for analysis.
