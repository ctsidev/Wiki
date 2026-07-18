# Useful Queries and Other Resources

*   [AD-POLST](#AD-POLST)
*   [Calculate Time on a Ventilator](#Calculate-Time-on-a-Ventilator)
*   [Health Maintenance Report](#Health-Maintenance-Report)
*   [Orders for Inpatient Transfers](#Orders-for-Inpatient-Transfers)
*   [BMT (Bone Marrow Transplant)](#)
*   [MyChart Activity](#MyChart-Activity)
    *   [MyChart Session Information (from EPIC docu)](#MyChartS)
    *   [MyChart Patient Status](#MyChart-Patient-Status)
*   [DEATH TABLE](#DEATH-TABLE)
*   [Face to Face Visits](#Face-to-Face-Visits)
*   [Chief Complaints](#Chief-Complaints)
*   [Current Med List](#Current-Med-List)
*   [Multiple Phone Numbers and Patient's preference](#Multiple-Phone-Numbers-and-Patient's-preference)
*   [Beaker Text Reports (narratives, etc...)](#Beaker-Text-Reports)
*   [Check Patient's Contact Information](#Check-Patient's-Contact-Information)
*   [All Flowsheet Questions on a Template](#All-Flowsheet-Questions-on-a-Template)
*   [Past Medical History](#Past-Medical-History)
*   [Procedure Performing and Billing Providers](#Procedure-Performing-and-Billing-Providers)
*   [Database Cleanup/Queries](#Database)
*   [Care Everywhere Labs](#Care-Everywhere-Labs)
*   [Occupation Information](#Occupation-Information)
    *   [Occupation](#Occupation)
*   [Health Maintenance Topics](#Health-Maintenance-Topics)
*   [Questionnaires and Answers](#Questionnaires-and-Answers)
*   [Address History (includes homeless)](#AddressH)
*   [Cause of Death](#CauseofD)
*   [GFR labs](#GFRlabs)
*   [Kill Sessions](#KillSess)
*   [Locked Objects](#LockedOb)
*   [Recruitment Request Prioritization](#Recruitment-Request-Prioritization)
*   [Bone Marrow Transplant (BMT)](#BoneMarr)
*   [Pregnancy/Delivery](#Pregnancy/Delivery)
*   [Calculate ICU stay times](#Calculate-ICU-stay-times)
*   [Glasgow Comma Score (GCS)](#GlasgowC)
*   [Sequential Organ Failure Assessment (SOFA)](#Sequenti)
*   [DENSE RANK in Oracle](#DENSE-RANK-in-Oracle)
    *   [How to pull first value in a query based on order rank](#Howtopul)
*   [Pathology HonestBroker Accession Numbers](#Pathology-HonestBroker-Accession-Numbers)
*   [Imaging HonestBroker Accession Numbers](#Imaging-HonestBroker-Accession-Numbers)
*   [Changing IDs and MRNs](#Changing-IDs-and-MRNs)
*   [Control Cohort Identification](#Control-Cohort-Identification)
*   [Vaccination](#Vaccination)
*   [ICU Care](#ICU-Care)
*   [SmartData Elements](#SmartData-Elements)
*   [Birth Control / Contraception / Sexually Active](#BirthCon)
*   [PHI disclosure HHS Guidelines](#PHI-disclosure-HHS-Guidelines)
*   [Provider Email](#Provider-Email)
*   [ICD-10-CM Updates to Lookup Table](#ICD-10-CM-Updates-to-Lookup-Table)
*   [Escape special characters in large texts](#Escape-special-characters-in-large-texts)
*   [Splitting table data into smaller files](#Splitting-table-data-into-smaller-files)
*   [Patient Set from I2B2 Query](#Patient-Set-from-I2B2-Query)
*   [Patient Set from User Patient List in Care Connect](#Patient-Set-from-User-Patient-List-in-Care-Connect)
*   [PROMIS Queries](#PROMIS-Queries)
*   [Jira Billing Hours](#Jira-Billing-Hours)
*   [Cancer Registrar Datamart METS](#Cancer-Registrar-Datamart-METS)
*   [Discharge Location](#Discharge-Location)
*   [Sex Assigned at Birth](#Sex-Assigned-at-Birth)
*   [Historical Addresses in MSSQL](#Historical-Addresses-in-MSSQL)
*   [PDF Scans from Clarity](#PDF-Scans-from-Clarity)
*   [PHQ9, PROMIS, SDOH, GAD7, AUDITC](#PHQ9,PRO)
*   [Dispensed Medications](#Dispensed-Medications)

## AD-POLST

###### (by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 6/26/19)

The following code identified the AD/POLST patient records from the scanned documents. It starts by creating a driver list, classifying it, pulling the scanned docs, and exporting them.

```sql
/**************************************

AD-POLST

***************************************/

-------------------------------------------

--Step 1: Create driver table for the AD/POLST doc types with final set of codes (use your own codes)

-------------------------------------------

DROP TABLE XDR_MEHTA_ADPOLST_DOCTYPE PURGE;
CREATE TABLE XDR_MEHTA_ADPOLST_DOCTYPE AS
SELECT *
FROM ZC_DOC_INFO_TYPE
where DOC_INFO_TYPE_C in (
   '11' -- Power of Attorney
   ,'300052' --Advance Directive Enduring
   ,'10' --Advance Directives and Living Will
   ,'200068' --DNR (Do Not Resuscitate) Documentation
   ,'300058' ---POLST
);

-------------------------------------------

--Step 2: Classify documents into AD or POLST category

-- Upon revision of your selection, your must group the different docs into these two categories

-------------------------------------------

ALTER TABLE XDR_MEHTA_ADPOLST_DOCTYPE ADD DOC_GROUP VARCHAR2(10);
UPDATE XDR_MEHTA_ADPOLST_DOCTYPE
SET DOC_GROUP = 'POLST'
WHERE
DOC_INFO_TYPE_C IN ('200068' -- DNR (Do Not Resuscitate) Documentation
   ,'300058' -- POLST);
);

COMMIT;

UPDATE XDR_MEHTA_ADPOLST_DOCTYPE
SET DOC_GROUP = 'AD'
WHERE
DOC_INFO_TYPE_C in (
   '11' -- Power of Attorney
   ,'300052' --Advance Directive Enduring
   ,'10' --Advance Directives and Living Will
);

COMMIT;

-------------------------------------------

-- Step 3: Pull all scanned docs

--

-------------------------------------------

DROP TABLE xdr_MEHTA_scan_docs PURGE;
CREATE TABLE xdr_MEHTA_scan_docs AS
SELECT distinct coh.pat_id
   ,coh.study_id
   ,bb.doc_info_id
   ,bb.doc_recv_time
   ,bb.SCAN_FILE
   ,bt.name as doc_type
   ,BT.DOC_GROUP
FROM xdr_MEHTA_PAT COH
join DOC_INFORMATION BB on coh.PAT_ID = BB.DOC_PT_ID
join XDR_MEHTA_ADPOLST_DOCTYPE BT on BB.DOC_INFO_TYPE_C = BT.DOC_INFO_TYPE_C
WHERE
   BB.IS_SCANNED_YN = 'Y'
   --and bb.doc_recv_time between sysdate - (365.25 *3 ) AND sysdate --(last three years);
   -- We noticed that in the case of AD/POLST documents, there were instances where the docs had been deleted
   and (bb.RECORD_STATE_C <> 2 OR bb.RECORD_STATE_C IS NULL)-- Deleted
   and (bb.DOC_STAT_C <> 35 OR bb.DOC_STAT_C IS NULL)-- Error
   and bb.DOC_REVOK_DT is null
   -- and bb.DOC_EXPIR_TIME is null
;

SELECT COUNT(*), COUNT(DISTINCT PAT_ID) FROM xdr_MEHTA_scan_docs;--179 110

-------------------------------------------

-- Step 4: Export

--

-------------------------------------------

SELECT DISTINCT PAT_ID
   ,STUDY_ID
   ,doc_recv_time
   ,DOC_GROUP
FROM xdr_MEHTA_scan_docs
WHERE doc_recv_time IS NOT NULL ;
```
## Calculate Time on a Ventilator

###### (by [Robert Follett (Unlicensed)](https://uclabip.atlassian.net/wiki/people/557058:fb90de8e-f6e8-4a11-b471-2992e0b019af?ref=confluence) on 11/06/2020)

This code will show you how to calculate ventilator time in days for a hospital stay.

The official method and MS SQL code sample is kept in Collibra here: [https://uclahealth.collibra.com/asset/cf559613-9b4d-4e73-831a-9429d41d83ef](https://uclahealth.collibra.com/asset/cf559613-9b4d-4e73-831a-9429d41d83ef)

```sql
/************************************************************************************************************************

For Ventilator data, restrict to Airway LDAs

************************************************************************************************************************/

create table xdr_PROJECT_airway_ids as
SELECT t.ID,
   t.CONTACT_DATE_REAL,
   gp.FLO_MEAS_NAME as LDA_NAME
FROM IP_FLO_LDA_TYPES t
JOIN IP_FLO_GP_DATA gp ON t.ID = gp.FLO_MEAS_ID
WHERE t.LDA_TYPE_OT_C = '11'; -- per Vihn Wells, validated by Dr. Neil Parikh

/************************************************************************************************************************

Get all Airway LDAs associated with discharged inpatient encounters

************************************************************************************************************************/

drop table xdr_PROJECT_airways purge;

create table xdr_PROJECT_airways as
SELECT ip_patient_id
   ,ip_enc_id
   ,enc.pat_enc_csn_id
   ,clenc.inpatient_data_id
   ,florow.LINE
   ,lda.IP_LDA_ID
   ,lda.FLO_MEAS_ID as LDA_FLO_MEAS_ID
   ,lda.PLACEMENT_INSTANT
   ,lda.REMOVAL_DTTM
   ,air.LDA_NAME
   ,enc.hosp_admsn_time
   ,enc.hosp_dischrg_time
FROM xdr_PROJECT_enc enc
join i2b2.lz_clarity_enc clenc on enc.pat_enc_csn_id = clenc.pat_enc_csn_id
JOIN IP_FLOWSHEET_ROWS florow ON clenc.Inpatient_Data_ID = florow.INPATIENT_DATA_ID
AND florow.IP_LDA_ID IS NOT NULL
JOIN IP_LDA_NOADDSINGLE lda ON florow.IP_LDA_ID = lda.IP_LDA_ID
JOIN xdr_PROJECT_airway_ids air ON lda.FLO_MEAS_ID = air.ID
AND lda.LDA_GROUP_CDR = air.CONTACT_DATE_REAL
;

/************************************************************************************************************************

Because Placement and Removal data is unreliable (manual entry), pull RecordedTime from assessment rows

- exclude LDAs with no assessment rows during the discharge encounter

- exclude assessment rows with RecordedTime before admission or after discharge

- exclude assessment rows before Placement, when that is documented

- exclude assessment rows after Removal, when that is documented

************************************************************************************************************************/

create table xdr_PROJECT_air_meas as
SELECT ip_patient_id,
   ip_enc_id,
   hosp_admsn_time,
   hosp_dischrg_time,
   ldarows.LDA_FLO_MEAS_ID,
   ldarows.LDA_NAME,
   ldarows.IP_LDA_ID,
   ldarows.PLACEMENT_INSTANT,
   ldarows.REMOVAL_DTTM,
   ldarows.INPATIENT_DATA_ID,
   ldarows.LINE as RowLine,
   flwrec.FSD_ID,
   meas.LINE,
   meas.FLO_MEAS_ID,
   meas.RECORDED_TIME,
   meas.MEAS_VALUE
FROM XDR_PROJECT_AIRWAYS ldarows
JOIN IP_FLWSHT_REC flwrec ON ldarows.INPATIENT_DATA_ID = flwrec.INPATIENT_DATA_ID
JOIN IP_FLWSHT_MEAS meas ON flwrec.FSD_ID = meas.FSD_ID
AND ldarows.LINE = meas.OCCURANCE
AND meas.MEAS_VALUE IS NOT NULL
WHERE
( meas.RECORDED_TIME >= ldarows.hosp_admsn_time )
AND ( ldarows.hosp_dischrg_time IS NULL OR meas.RECORDED_TIME <= ldarows.hosp_dischrg_time )
AND ( ldarows.PLACEMENT_INSTANT IS NULL OR meas.RECORDED_TIME >= ldarows.PLACEMENT_INSTANT )
AND ( ldarows.REMOVAL_DTTM IS NULL OR meas.RECORDED_TIME <= ldarows.REMOVAL_DTTM );

/************************************************************************************************************************

Calculate time for each Airway LDA

************************************************************************************************************************/

create table xdr_PROJECT_ventminmax as

SELECT ip_enc_id, IP_LDA_ID,
   MIN (hosp_admsn_time) as HospitalAdmission,
   MAX (hosp_dischrg_time) as HospitalDischarge,
   MIN (PLACEMENT_INSTANT) as PlacementTime,
   MAX (REMOVAL_DTTM) as RemovalTime,
   MIN (RECORDED_TIME) as MinRecorded,
   MAX (RECORDED_TIME) as MaxRecorded
FROM xdr_PROJECT_air_meas
GROUP BY ip_enc_id, IP_LDA_ID;

drop table xdr_PROJECT_venttime purge;

create table xdr_PROJECT_venttime as
select ip_enc_id,
   round(sum(airwaydays),1) as total_vent_days
from (
SELECT ip_enc_id,
IP_LDA_ID,
MaxTimePerLDA - MinTimePerLDA as AirwayDays
FROM (
   SELECT v.*,
   CASE
   WHEN PlacementTime IS NULL THEN MinRecorded
   WHEN PlacementTime < HospitalAdmission THEN MinRecorded
   WHEN PlacementTime <= MinRecorded THEN PlacementTime
   ELSE MinRecorded
   END as MinTimePerLDA,
   CASE
   WHEN RemovalTime IS NULL THEN MaxRecorded
   WHEN RemovalTime > HospitalDischarge THEN MaxRecorded
   WHEN RemovalTime >= MaxRecorded THEN RemovalTime
   ELSE MaxRecorded
   END as MaxTimePerLDA
   FROM xdr_PROJECT_ventminmax v
) x)
y
group by ip_enc_id;

select * from XDR_PROJECT_VENTTIME;
```
## Health Maintenance Report

###### (by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 6/26/19)

This code allows to access historical data from the Health Maintenance Report. We just have to use the date given by the PI in reference to the extract_date in the hm report, and use the last entry to find the corresponding the status and IDEAL_RETURN_DT each time.

Example: We must apply the selection for Colonoscopy screening:

Galaxy link: [https://datahandbook.epic.com/Reports/Details/1239812?rank=1&queryid=63476074&docid=87831](https://datahandbook.epic.com/Reports/Details/1239812?rank=1&queryid=63476074&docid=87831)
```sql
**Table**: **F_HM_TREND**, The fact table is a derived table that stores Health Maintenance information.  
This table is intended to take monthly snapshots to allow overtime trending on Health Maintenance topic data.  
Will allow for facility, department and provider level reports. This is an append-only table that does not make  
use of Start or End dates. [https://datahandbook.epic.com/ClarityDictionary/Details?tblName=F_HM_TREND](https://datahandbook.epic.com/ClarityDictionary/Details?tblName=F_HM_TREND)

##### **Health Maintenance Report**

-- The cohort table includes a reference date to use when pulling the health maintenance at a particular point in time

select distinct coh.pat_id
   ,coh.reference_date
   ,hm.extract_date
   ,hm.TOPIC_NAME
   ,hm.IDEAL_RETURN_DT
   ,zst.name as due_status_dt1
   ,MAX(hm.extract_date) OVER (PARTITION BY coh.pat_id) AS latest_measure
from xdr_prinv_coh coh
left JOIN F_HM_TREND hm ON coh.pat_id = hm.pat_id and trunc(hm.extract_date) <= coh.date_1
left join ZC_HMT_DUE_STATUS zst ON hm.HMT_DUE_STATUS_C = zst.HMT_DUE_STATUS_C
where
hm.QUALIFIED_HMT_ID = 7770000132 --Colon Ca Screening: COLONOSCOPY
) x
WHERE x.extract_date = latest_measure
) dt1 on coh.pat_id = dt1.pat_id;

# Orders for Inpatient Transfers

###### (by [Robert Follett (Unlicensed)](https://uclabip.atlassian.net/wiki/people/557058:fb90de8e-f6e8-4a11-b471-2992e0b019af?ref=confluence) on 7/02/19)

This code will pull orders to admit a patient into the hospital.

##### **Inpatient Orders**

select op.pat_id,
   op.pat_enc_csn_id,
   admit_ser.prov_name as admit_prov_name,
   op.order_time,
   op.display_name,
   auth_ser.prov_name as auth_prov_name,
   op.proc_id,
   op.ORD_CREATR_USER_ID
from order_proc op
join clarity_eap eap on op.proc_id = eap.proc_id
join clarity_ser admit_ser on op.ORD_CREATR_USER_ID = admit_ser.user_id
join clarity_ser auth_ser on op.authrzing_prov_id = auth_ser.prov_id
where op.proc_id in (263, 327411, 327400,721947) -- Admit to IP
and op.order_status_c = 5
order by op.pat_id, op.order_time;
```
# BMT: Bone Marrow Transplant

###### (by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 7/10/19)

I have found some resources on how to find information for Bone Marrow Transplants. Since it's not one of the types in \[**TX_CLASS_C**\], the key is to use \[**SUM_BLK_TYPE_ID**\] = 2050001100 "HSCT ALLOGENEIC RECIPIENT"

Inexplicably, this item is not in \[**ZC_SUM_BLK_TYPE**\] but Ahson Shigri (the person that Star referred me to for transplant questions) told me about it.
```sql
##### **BMT counts**

select count(distinct pat.pat_id)
from patient pat
join pat_enc enc on pat.pat_id = enc.pat_id
join Episode_Link lnk on enc.pat_enc_csn_id = lnk.pat_enc_csn_id
join Episode ep on lnk.episode_id = ep.episode_id
left join BMT_INFO bmt on ep.episode_id = bmt.SUMMARY_BLOCK_ID
where
lnk.SUM_BLK_TYPE_ID = 2050001100 ----2050001100 "HSCT ALLOGENEIC RECIPIENT"
--join TRANSPLANT_INFO inf on ep.episode_id = inf.SUMMARY_BLOCK_ID --nothing comes from this join
```
# MyChart Activity

###### (by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 7/17/19)

The final solution was to pull directly from

[**MYC_PT_USER_ACCSS**](https://datahandbook.epic.com/ClarityDictionary/Details?tblName=MYC_PT_USER_ACCSS): This table stores an audit trail of actions taken within patient facing web applications. This includes MyChart, MyChart Bedside, and audits for web services intended for use by patient facing applications. MyChart Bedside events will populate WPR 535 instead of WPR 530 to indicate the type of audit event. The fields in this table track who took the action (typically the patient), what action was taken and when the action was taken.

*   **UA_WHO_ACCESSED:** If a patient accessed their own account, then this field stores that person's WPR ID (WPR .1). It may be the patient or another person who has proxy access to the patient's account.
*   **MYPT_ID:** The unique ID of the WPR user record that was accessed. The WPR user could be a user of MyChart or a MyChart Bedside user.
*   **This means that if a mother(proxy) accessed her child’s account. The mom’s mypt_id was recorded in this field, while the child’s was stored under mypt_id. By comparing these two, we can know if the access was the patient herself, or a proxy.**

Additionally, the [**MYC_PATIENT**](https://datahandbook.epic.com/ClarityDictionary/Details?tblName=MYC_PATIENT) includes a field called **PROXY_ACCOUNT_YN** that flags if the patient has a proxy account authorized to access his/her info.

The table [**PAT_MYC_PRXY_HX**](https://datahandbook.epic.com/ClarityDictionary/Details?tblName=PAT_MYC_PRXY_HX) contains a historical record of all the proxies for any given patient. The field **PROXY_PAT_ID**: The unique ID of the patient who has proxy access to the record of the patient who is identified in **PAT_ID**
```sql
**Field** 

##### **MyChart Activity**

SELECT distinct coh.pat_id
   ,MYC.MYPT_ID
   ,myc.PROXY_ACCOUNT_YN
   ,PRX.PROXY_PAT_ID
   ,mycPRX.MYPT_ID AS PROXY_MYPT_ID
   ,act.UA_WHO_ACCESSED
   ,CASE WHEN MYC.MYPT_ID = act.UA_WHO_ACCESSED THEN 0 ELSE 1 END PROXY_ACCESS_YN
   ,act.UA_SESSION_NUM
   ,act.myc_ua_type_c
   ,act.MYC_UA_ACTION_C
   ,act.BEDSIDE_UA_TYPE_C
   ,ztyp.name as type
   ,zact.name as action
   ,act.UA_PERSON_TYPE_C
   ,act.ua_time
   ,act.UA_EXTENDED_INFO
FROM xdr_prinv_coh COH
LEFT JOIN PAT_MYC_PRXY_HX PRX ON COH.PAT_ID = PRX.PAT_ID
JOIN MYC_PATIENT myc ON coh.pat_id = myc.pat_id
LEFT JOIN MYC_PATIENT mycPRX ON PRX.PROXY_PAT_ID = mycPRX.pat_id
JOIN MYC_PT_USER_ACCSS ACT ON MYC.MYPT_ID = ACT.MYPT_ID
AND act.MYC_UA_TYPE_C IS NOT NULL
LEFT JOIN ZC_MYC_UA_TYPE ztyp ON act.myc_ua_type_c = ztyp.myc_ua_type_c
LEFT JOIN ZC_MYC_UA_ACTION zact ON act.MYC_UA_ACTION_C = zact.MYC_UA_ACTION_C
LEFT JOIN ZC_BEDSIDE_UA_TYPE zbed ON act.BEDSIDE_UA_TYPE_C = zbed.BEDSIDE_UA_TYPE_C
WHERE

-- Date range of interest for patient portal activity is 8/1/17 - 7/31/18.

TRUNC(act.ua_time) BETWEEN '08/01/2015' AND '07/31/2018'
AND act.UA_PERSON_TYPE_C = 1--patient
;

If you need to lookup the session information, you should resort to [**V_MYC_SESSIONS:**](https://datahandbook.epic.com/ClarityDictionary/Details?tblName=V_MYC_SESSIONS) This view should be used as the base table for reporting content that reports on the MyChart Session because it handles required filtering on the derived table underlying the view query. This view can be joined to MYC_PT_USER_ACCSS for detailed information about what the user did during their MyChart session by using queries of the form outlined below.

*   *   **PROXY_ACCESS_BOOL**: This column is equal to 1 if the user who logged into MyChart accessed information about another MyChart user(proxy subject) during the session. Join this table to MYC_PT_USER_ACCSS as described in the table description for detailed information on the proxy  
        subject(s) accessed during the session. _(After doing some work on the proxy access, I encountered this field not to be completely accurate and you should resort to the previous query to draw conclusions in this regard)_
    *   **MYPT_ID**: If a patient accessed their own account, then this field stores that person's WPR ID (WPR .1). It may be the patient or another person who has proxy access to the patient's account.
    *   **PAT_ID**: If the user who logged into MyChart is a patient, then this column contains a PAT_ID, commonly used to join to the PATIENT table. If the user who logged into MyChart is a non-patient user, then this column is NULL. Because of this, INNER JOIN may not always be the appropriate join type for linking to the PATIENT table.

##### **MyChart Session Information (from EPIC docu)**

SELECT * FROM V_MYC_SESSIONS INNER JOIN MYC_PT_USER_ACCSS ON MYC_PT_USER_ACCSS.UA_WHO_ACCESSED = F_MYC_SESSIONS.MYPT_ID AND MYC_PT_USER_ACCSS.UA_SESSION_NUM = F_MYC_SESSIONS.UA_SESSION_NUM

The query belows check the latest MyChart status of a patient.

##### **MyChart Patient Status**

select distinct pat_id
   ,end_dttm
   ,mychart_status_c
from f_pat_mychart_status_hx
where end_dttm is null --this indicates the latest MyChart status of a patient
and mychart_status_c = 1 --this indicates that the latest MyChart status of a patient is "active"
;

/*

PREVIOUS LOGIC (replaced by query above)

If you need to check if a patient is active in MyChart:

query i2b2.lz_clarity_pat_contacts and check if active_mychart is "Yes"

or check clarity.patient_myc directly for mychart_status_c

or check clarity.myc_patient directly for status_cat_c

select distinct coh.pat_id
   ,active_mychart
   ,pmc.mychart_status_c
   ,zpmc.name patient_myc_status
   ,mcp.status_cat_c
   ,zmcp.name myc_patient_status
from xdr_prinv_coh coh
left join i2b2.lz_clarity_pat_contacts con on coh.pat_id = con.pat_id
left join clarity.patient_myc pmc on coh.pat_id = pmc.pat_id
left join clarity.zc_alt_webste_stat zpmc on pmc.mychart_status_c = zpmc.alt_webste_stat_c
left join clarity.myc_patient mcp on coh.pat_id = mcp.pat_id
left join clarity.zc_myc_status zmcp on mcp.status_cat_c = zmcp.myc_status_c
-- the criteria below selects only active MyChart patients
-- where nvl(con.active_mychart,'No') = 'Yes' --default null to inactive
-- or nvl(pmc.mychart_status_c,2) = 1 --default null to inactive
-- or nvl(mcp.status_cat_c,2) = 1 --default null to inactive
;

*/
```
## DEATH TABLE

[Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence)

We decided to create a death table to capture cases where patients, that do not have a "deceased" status in Care Connect, that have been found to be deceased through other venues. For instance, a PI team contacting patients for enrollment is told by a relative or their PCP that the patient is dead. Additionally, we are planning on adding those patients in OHIA's death registry once we determine the final validation process, aimed at finding which ones are very likely to be deceased, even though there is no final confirmation in Care Connect yet.
```sql
##### **DEATH TABLE CREATION**

DROP TABLE I2B2.LZ_DEATH PURGE;

CREATE TABLE LZ_DEATH
("PAT_ID" VARCHAR2(10 BYTE)
,"SOURCE" VARCHAR2(256 BYTE) --Example: 'ACP - Project, IRB 18-001612'
,"USER" VARCHAR2(15 BYTE) --Example: 'JSANZ'
,CREATE_DATE TIMESTAMP --use SYSDATE
,"COMMENT" VARCHAR2(1256 BYTE) --Example: 'The patients husband reported her to be deceased when receiving a notification from the research team on early September 2019. The PI team let us know'
,"DEATH_DATE" DATE); --LEave it blank if we don't have it

We shall incorporate this table to the death function so that it's taken into account when producing the most up to date vital status for each patient. 

[Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) (can you implement the function part?) The table has been created already. Thanks

Hi, [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence), are we planning to have a death date in this table?  Thanks!

# Face to Face Visits

###### (by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 9/5/19)

([Robert Follett (Unlicensed)](https://uclabip.atlassian.net/wiki/people/557058:fb90de8e-f6e8-4a11-b471-2992e0b019af?ref=confluence) - Added telemedicine and telephone because of Covid)

The following identifies face-to-face visits.

1.  visit_type = 'AV'
    1.  encounter_date <= sysdate
    2.  nvl(appointment_status,'Completed') = 'Completed'
2.  visit_type != 'AV'
    1.  encounter_date <= sysdate
    2.  appointment_status = 'Completed'
    3.  encounter types listed in face-to-face encounter types sheet (I:\\_BIP\\xDR\\Lists and Codes\\Face to Face Visits.xlsx)

##### **Face to Face Visits Query**

FROM i2b2.lz_clarity_enc e
JOIN i2b2.lz_enc_visit_types vt ON e.pat_enc_csn_id = vt.pat_enc_csn_id
WHERE e.effective_date_dt < SYSDATE --Completed appts are prior to current date
AND ((vt.visit_type = 'AV' --Ambulatory Visit
AND (e.appt_status_c IS NULL OR e.appt_status_c = '2') --Completed (includes null)
)
OR
(vt.visit_type <> 'AV' --Non-Ambulatory Visit
AND e.appt_status_c = '2' --Completed (does not include null)
AND e.enc_type_c IN ('1000' --Initial consult --Face-to-face encounter types per I:\\_BIP\\xDR\\Lists and Codes\\Face to Face Visits.xlsx
,'1001' --Anti-coag visit
,'1003' --Procedure visit
,'101' --Office Visit
,'108' --Immunization
,'112540' --Home Visit
,'1200' --Routine Prenatal
,'1201' --Initial Prenatal
,'139101' --Ophthalmology Procedure
,'201' --Nurse/Clinical Support
,'202' --Social Work
,'209' --Education
,'210' --Treatment
,'2100' --Surgical Consult
,'2101' --Clinical Support
,'2501' --Evaluation
,'2502' --Follow-Up
,'2508' --Infusion
,'2510' --Pre-op/Pre-procedure Orders
,'2535' --DEXA Scan
,'2543' --Integrated Practice Office Visit
,'3' --Hospital Encounter
,'49' --Lactation Encounter
,'50' --Appointment
,'55' --Ancillary Procedure
--,'70' --Telephone (Remove per DB on 3/14/2022)
,'76' --Telemedicine
--,'21076' --Tele-Medicine (Remove per DB on 3/14/2022)
)
))

# Chief Complaints

PAT_ENC_RSN_VISIT. ENC_REASON_NAME obtains the chief complaints for the cohort.

Modified on 06/07/2021 by Rob Follett - Added the CL_RSN_FOR_VISIT lookup table.

left join (select distinct lk.pat_mrn_id as mrn
,vs.pat_enc_csn_id
,listagg(rfv.display_text, '||') within group (order by vs.line) as chief_complaint_list
from xdr_nnnnn_cohort lk
join clarity.pat_enc_rsn_visit vs on lk.pat_id = vs.pat_id
join CL_RSN_FOR_VISIT rfv on vs.enc_reason_id = rfv.reason_visit_id
group by lk.pat_mrn_id,vs.pat_enc_csn_id
) chief on cohort.pat_enc_csn_id = chief.pat_enc_csn_id

--TT-9/26/2023: Alternate query

left join (select pat_enc_csn_id, listagg(trim(enc_reason_name), '|') within group (order by pat_enc_csn_id, line) as chief_complaint_list
from (select distinct vs.pat_id, vs.pat_enc_csn_id, vs.line, vs.enc_reason_name
from p_coh lk
join clarity.pat_enc_rsn_visit vs on lk.pat_id = vs.pat_id)
group by pat_enc_csn_id
) chief on coh.pat_enc_csn_id = chief.pat_enc_csn_id

# Current Med List

[Robert Follett (Unlicensed)](https://uclabip.atlassian.net/wiki/people/557058:fb90de8e-f6e8-4a11-b471-2992e0b019af?ref=confluence)

PAT_ENC_CURR_MEDS table enables you to report on current (as well as active) medications per encounter as listed in clinical system.

##### **Current Med List**

--Pull the latest entries in PAT_ENC_CURR_MEDS, then reject if inactive or historical.

-- If active flag is null, treat as active.

select x.*
,m.*
from (select distinct cm.pat_id
,cm.current_med_id
,cm.contact_date
,cm.is_active_yn
,max(cm.contact_date) over (partition by cm.pat_id) latest_contact_date
from pat_enc_curr_meds cm
where nvl(cm.is_active_yn,'Y') = 'Y'
) x
join i2b2.lz_clarity_meds m on x.current_med_id = m.order_med_id
where x.contact_date = x.latest_contact_date
and m.order_class <> 'Historical Med'
order by x.pat_id, x.current_med_id
;

# Multiple Phone Numbers and Patient's preference

###### (by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 9/25/19)

The following code finds the multiple phone numbers for each patients, check the patient's preference and merge it into the output.

The result is an ordered list of phone numbers for each patient base on the patient's preference, and a field compiling all preferences into one string

| field | sample | description |
| --- | --- | --- |
| PAT_ID | Z123456 |  |
| PHONE1 | 310-123-4567 |  |
| CONTACTPRIORITY1 | H1 | Phone1 is the Home (H) phone number and it was set up as priority one by the patient |
| PHONE2 | 818-123-4567 |  |
| CONTACTPRIORITY2 | M2 | Phone2 is the Mobile(M) phone number and it was set up as priority two by the patient |
| PHONE3 | 310-321-6547 |  |
| CONTACTPRIORITY3 | W0 | Phone3 is the Work(W) phone number and it had no priority set up by the patient |
| PHONE4 | 312-1234567 |  |
| CONTACTPRIORITY4 | H0 | Phone4 is the Home(h) phone number and it had no priority set up by the patient |
| CONTACTPRIORITYORDER_PHONE1234 | H1 | M2 | W0 | H0 | Home 1st priority - Mobile 2nd priority - Work No priority - Home No priority |

##### **Multiple Phone Number and Patient's Preference**

/* trying to convert multiple rows/columns into columns per patient using pivot and unpivot

phone numbers have multiple values and have to incorporate contact priority

first using unpivot - making all phone numbers and contact priority into multiple rows and

then using pivot - converting values with multiple rows into multiple columns per patient

*/

with home_result as

(

Select coh.pat_ID,
   othcom.name as phone_type,
   cm1.OTHER_COMMUNIC_NUM as phone ,
   cm1.line,
   case when cm1.CONTACT_PRIORITY is not null then cm1.CONTACT_PRIORITY else 99 end as CONTACT_PRIORITY,
   concat(SUBSTR( othcom.name, 1 , 1 ), (case when cm1.CONTACT_PRIORITY is not null then cm1.CONTACT_PRIORITY else 0 end )) as Contactpriorityorder
from XDR_PRINV_COH coh
inner join OTHER_COMMUNCTN cm1 on coh.pat_id = cm1.pat_id --and cm1.OTHER_COMMUNIC_C = 7 --Home Phone
left outer join ZC_OTHER_COMMUNIC othcom on othcom.OTHER_COMMUNIC_C = cm1.OTHER_COMMUNIC_C
/* if multiple values, grouping them at patient level */
WHERE
-- Exclude obvious place holders or invalid phone #s
cm1.OTHER_COMMUNIC_NUM is not null
and cm1.OTHER_COMMUNIC_NUM not in ('000-000-0000'
,'000-000-0001'
,'999-999-9999'
,'310-000-0000'
,'818-999-9999'
,'310-000-0001'
,'805-000-0000'
,'213-000-0001'
)
)

SELECT
PAT_ID
,"'PHONE_1'" as "phone1"
,"'CONTACTPRIORITYORDER_2'" AS "contactpriority_phone1"
,"'PHONE_3'" AS "phone2"
,"'CONTACTPRIORITYORDER_4'" AS "contactpriority_phone2"
,"'PHONE_5'" AS "phone3"
,"'CONTACTPRIORITYORDER_6'" AS "contactpriority_phone3"
,"'PHONE_7'" AS "phone4"
,"'CONTACTPRIORITYORDER_8'" AS "contactpriority_phone4"
,CONCAT(
CONCAT(
CONCAT(CASE WHEN "'CONTACTPRIORITYORDER_2'" IS NOT NULL THEN "'CONTACTPRIORITYORDER_2'" ELSE 'NA' END, '| ')
,CONCAT(CASE WHEN "'CONTACTPRIORITYORDER_4'" IS NOT NULL THEN "'CONTACTPRIORITYORDER_4'" ELSE 'NA' END, '| ' )
)
,CONCAT(
CONCAT(CASE WHEN "'CONTACTPRIORITYORDER_6'" IS NOT NULL THEN "'CONTACTPRIORITYORDER_6'" ELSE 'NA' END, '| ' )
,CASE WHEN "'CONTACTPRIORITYORDER_8'" IS NOT NULL THEN "'CONTACTPRIORITYORDER_8'" ELSE 'NA' END)
)
AS "contactpriorityorder_phone1234"
FROM (
SELECT PAT_ID
,concat(
concat( col , '_'), ROW_NUMBER() OVER (PARTITION BY PAT_ID ORDER BY orderpriority ASC)
) AS col
,value
FROM (
SELECT PAT_ID
,cast ( phone as varchar2(30)) as phone
,cast(Contactpriorityorder as varchar2(30)) as Contactpriorityorder
, ROW_NUMBER() OVER (PARTITION BY PAT_ID ORDER BY CONTACT_PRIORITY ASC) as orderpriority
FROM home_result
GROUP BY PAT_ID, phone, Contactpriorityorder, CONTACT_PRIORITY
) rt
unpivot ( value FOR col in ( phone, Contactpriorityorder))unpiv
) tp
pivot ( Max(value) FOR col in ('PHONE_1'
,'CONTACTPRIORITYORDER_2','PHONE_3','CONTACTPRIORITYORDER_4',
'PHONE_5','CONTACTPRIORITYORDER_6','PHONE_7','CONTACTPRIORITYORDER_8'
)
) piv
order by 1;
```
## Beaker Text Reports

###### (BY [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) ON 9/26/19)

The following code pulls the text based results (comments, narratives, etc...) using the case_id as a reference. A common use case for this resource is when the usual query returns "This result contains rich text formatting which cannot be displayed here" as a result. The following script is the original code sent to us. Below it, you can find a more specific application of it.

This script can pull results after January 2016 (Beaker) for previous results this might not work
```sql
##### **Original Query**

WITH
CaseIDsToProcess AS (
SELECT LAB_CASE_DB_MAIN.CASE_ID
FROM LAB_CASE_DB_MAIN
WHERE CASE_ID IN (818130 )
),ExternalPatientInfo AS (
SELECT REQ_GROUPER_ID, REQ_SUBMITTER_ID, REQ_SUBM_PT_ID, REQ_INTERVAL_NUMBER, REQ_ACCOUNT_ID, REQ_CHART_NUM, EXTERNAL_VISIT_ID FROM (
SELECT DISTINCT REQ_GROUPER_ID, REQ_SUBMITTER_ID, REQ_SUBM_PT_ID, REQ_INTERVAL_NUMBER, REQ_ACCOUNT_ID, REQ_CHART_NUM, EXTERNAL_VISIT_ID,
RANK() OVER (PARTITION BY REQ_GROUPER_ID, REQ_SUBMITTER_ID, REQ_INTERVAL_NUMBER, REQ_ACCOUNT_ID, REQ_CHART_NUM ORDER BY REquisition_ID DESC, NVL(EXTERNAL_VISIT_ID,'-') DESC, REQ_SUBM_PT_ID) as Rankorder
FROM CLARITY.REQ_DB_MAIN
WHERE
REQ_DB_MAIN.REQUISITION_ID IN (SELECT CASE_ID FROM CaseIDsToProcess )
AND REQ_SUBMITTER_ID IS NOT NULL) REQDBMAINResults
WHERE REQDBMAINResults.RankOrder=1
),

PatientInformation AS (
SELECT
DISTINCT
CASE WHEN NVL(REQ_DB_MAIN.REQ_GROUPER_ID,0) <>0 THEN CAST (NVL(REQ_DB_MAIN.REQ_GROUPER_ID,'0') AS VARCHAR2(25)) ELSE PATIENT.PAT_ID END || '-' ||LAB_CASE_DB_MAIN.CASE_ID uniqueid,
LAB_CASE_DB_MAIN.CASE_ID
FROM CaseIDsToProcess
INNER JOIN CLARITY.LAB_CASE_DB_MAIN ON CaseIDsToProcess.CASE_ID=LAB_CASE_DB_MAIN.CASE_ID
LEFT JOIN CLARITY.PATIENT ON PATIENT.PAT_ID=LAB_CASE_DB_MAIN.CASE_PAT_ID
LEFT JOIN CLARITY.REQ_CASES ON REQ_CASES.CASES_ID = CaseIDsToProcess.CASE_ID
LEFT JOIN CLARITY.REQ_DB_MAIN ON REQ_DB_MAIN.REQUISITION_ID = CLARITY.REQ_CASES.REQUISITION_ID
LEFT JOIN CLARITY.RQG_DB_MAIN ON REQ_DB_MAIN.REQ_GROUPER_ID = RQG_DB_MAIN.RQG_GROUPER_ID
LEFT JOIN CLARITY.SPEC_DB_MAIN ON LAB_CASE_DB_MAIN.CASE_ID=SPEC_DB_MAIN.CASE_ID
LEFT JOIN CLARITY.LAB_SMT_NOADD ON SPEC_DB_MAIN.REQ_SMT_ID = Lab_Smt_Noadd.Record_Id
LEFT JOIN CLARITY.REQ_ORDER_GROUP ON REQ_ORDER_GROUP.REQUISITION_ID=LAB_CASE_DB_MAIN.CASE_ID
LEFT JOIN CLARITY.ORDER_PROC ON REQ_ORDER_GROUP.ORDER_ID=ORDER_PROC.ORDER_PROC_ID
LEFT JOIN ExternalPatientInfo ON REQ_DB_MAIN.REQ_GROUPER_ID = ExternalPatientInfo.REQ_GROUPER_ID
),

PatientCases AS
(
SELECT
DISTINCT
PatientInformation.uniqueid,
LAB_CASE_INFO.CASE_NUM,
LAB_CASE_DB_MAIN.CASE_ID,
LAB_CASE_INFO.AP_CASE_STATUS_C
FROM CaseIDsToProcess
INNER JOIN CLARITY.LAB_CASE_DB_MAIN ON CaseIDsToProcess.CASE_ID=LAB_CASE_DB_MAIN.CASE_ID
INNER JOIN CLARITY.LAB_CASE_INFO ON LAB_CASE_INFO.REQUISITION_ID = LAB_CASE_DB_MAIN.CASE_ID
INNER JOIN CLARITY.ZC_AP_CASE_STATUS ON ZC_AP_CASE_STATUS.AP_CASE_STATUS_C = LAB_CASE_INFO.AP_CASE_STATUS_C
INNER JOIN PatientInformation ON CaseIDsToProcess.CASE_ID=PatientInformation.Case_Id
),

CASERESULT AS (
SELECT
UNIQUEID,
CASE_ID,
CASE_NUM,
RESULT_ID,
AP_CASE_STATUS_C,
RES_VAL_STATUS_C,
Line,
Value_Line,
COMPONENT_ID,
EXTERNAL_NAME,
REPLACE(REPLACE(MULT_LN_VAL_STORAGE,'\\','\\\\'),'"','\\"') AS ResultText,
CMP_MULTILINE_VALUE
FROM

(
SELECT DISTINCT
h.uniqueid,
a.Case_ID,
h.CASE_NUM,
c.RESULT_ID,
d.COMPONENT_ID,
NVL(e.CMP_MULTILINE_VALUE,1) AS CMP_MULTILINE_VALUE,
NVL(d.LINE,1) AS LINE,
NVL(f.VALUE_LINE,1) AS VALUE_LINE ,
g.EXTERNAL_NAME,
NVL(f.MULT_LN_VAL_STORAGE,NVL(d.COMPONENT_RESULT,'')) AS "MULT_LN_VAL_STORAGE",
RANK() OVER ( Partition BY a.CASE_ID Order by c.Result_ID DESC) AS "RANK",
c.RES_VAL_STATUS_C,
h.AP_CASE_STATUS_C
FROM CaseIDsToProcess a
, CLARITY.SPEC_DB_MAIN b
, CLARITY.RES_DB_MAIN c
, CLARITY.Res_Components d
, CLARITY.RES_VAL_PTR_RM e
, CLARITY.RES_VAL_DATA_RM f
, CLARITY.CLARITY_COMPONENT g
, PatientCases h
WHERE
a.CASE_ID=b.CASE_ID
AND a.CASE_ID=h.CASE_ID
AND b.SPECIMEN_ID = c.RES_SPECIMEN_ID
AND c.RESULT_ID = d.RESULT_ID
AND d.LINE = e.GROUP_LINE (+)
AND d.RESULT_ID = e.RESULT_ID (+)
AND e.CMP_MULTILINE_VALUE = f.GROUP_LINE (+)
AND e.RESULT_ID = f.RESULT_ID (+)
AND d.COMPONENT_ID= g.COMPONENT_ID
-- AND c.RES_VAL_STATUS_C = 9
AND d.COMPONENT_REPORT_YN=1
AND d.COMPONENT_ID <> 7100796
) RESULT
WHERE RANK=1
AND MULT_LN_VAL_STORAGE IS NOT NULL
)SELECT UNIQUEID,CASE_ID,CASE_NUM, RESULT_ID,AP_CASE_STATUS_C,RES_VAL_STATUS_C,Line,Value_Line,COMPONENT_ID,EXTERNAL_NAME,ResultText,CMP_MULTILINE_VALUE FROM CaseResult ORDER BY UNIQUEID, LINE, CMP_MULTILINE_VALUE, VALUE_LINE;

##### **Pull Beaker Results - Use Case**

/**************************************************

Pull the orders for your cohort

---------------------------------------------------

Use any selections that might apply to your particular case

**************************************************/

DROP TABLE xdr_prinv_opr PURGE;

CREATE TABLE xdr_prinv_opr AS
SELECT DISTINCT pat.pat_id
   ,pat.pat_mrn_id
   ,pat.study_id
   ,opr.order_proc_id
   ,opr.proc_id
   ,opr.description
   ,eap.proc_name
   ,opr.proc_code
   ,opr.order_time
   ,opr.result_time
   ,opr.order_type_c
   ,xot.NAME AS order_type
   ,opr.abnormal_yn
   ,opr.order_status_c
   ,opr.radiology_status_c
   ,opr.specimen_source_c
   ,opr.specimen_type_c
   ,acc.acc_num
   ,xpt.NAME AS specimen_type
   ,res.line
   ,res.ord_value
   ,res.component_id
   ,cmp.NAME AS component_name
   ,res.component_comment
   ,res.result_time AS res_result_time
   ,res.result_val_start_ln
   ,res.result_val_end_ln
   ,res.result_cmt_start_ln
   ,res.result_cmt_end_ln
   ,res.lrr_based_organ_id
FROM xdr_prinv_enc pat
JOIN order_proc opr ON pat.pat_enc_csn_id = opr.pat_enc_csn_id
LEFT JOIN order_results res ON opr.order_proc_id = res.order_proc_id
LEFT JOIN order_rad_acc_num acc ON opr.order_proc_id = acc.order_proc_id
LEFT JOIN clarity_eap eap ON opr.proc_id = eap.proc_id
LEFT JOIN clarity_component cmp ON res.component_id = cmp.component_id
LEFT JOIN zc_order_type xot ON opr.order_type_c = xot.order_type_c
LEFT JOIN zc_specimen_type xpt ON opr.specimen_type_c = xpt.specimen_type_c
WHERE opr.order_status_c = 5
-- for referrals, see comments above for order_status_c IN (2,4)
--AND opr.order_type_c NOT IN (7,26,62,63) --Not labs --not really necessary when a selection is made in the following lines
--AND (opr.order_type_c IN (5,1003) OR opr.radiology_status_c = 99) --Imaging
--AND (opr.order_type_c = 3) --Microbiology
--AND (opr.order_type_c = 59) --Pathology
--AND (opr.order_type_c = 8) --Outpatient Referral
;

/**************************************************

After you investigate the specific procedures/components

that apply to your case, pull the case_id's related

to them

**************************************************/

DROP TABLE xdr_prinv_opr_selection PURGE;

CREATE TABLE xdr_prinv_selection AS
select DISTINCT
-- requisition_id matches the case_id
cas.requisition_id
,opr.*
from XDR_ABDALLA_OPR_SAMPLE opr
JOIN LAB_CASE_INFO cas ON opr.acc_num = cas.CASE_NUM -- this is added join that allows to find the case_idwhere
opr.proc_id = '77929'
and opr.component_id = '7000004'
;

/**************************************************

Investigate the specific procedures/components

that apply to your case

**************************************************/

drop table xdr_prinv_opr_results purge;

create table xdr_prinv_opr_results as
WITH
CaseIDsToProcess AS (
SELECT requisition_id as CASE_ID
FROM xdr_prinv_selection
)
),ExternalPatientInfo AS (
SELECT REQ_GROUPER_ID, REQ_SUBMITTER_ID, REQ_SUBM_PT_ID, REQ_INTERVAL_NUMBER, REQ_ACCOUNT_ID, REQ_CHART_NUM, EXTERNAL_VISIT_ID FROM (
SELECT DISTINCT REQ_GROUPER_ID, REQ_SUBMITTER_ID, REQ_SUBM_PT_ID, REQ_INTERVAL_NUMBER, REQ_ACCOUNT_ID, REQ_CHART_NUM, EXTERNAL_VISIT_ID,
RANK() OVER (PARTITION BY REQ_GROUPER_ID, REQ_SUBMITTER_ID, REQ_INTERVAL_NUMBER, REQ_ACCOUNT_ID, REQ_CHART_NUM ORDER BY REquisition_ID DESC, NVL(EXTERNAL_VISIT_ID,'-') DESC, REQ_SUBM_PT_ID) as Rankorder
FROM CLARITY.REQ_DB_MAIN
WHERE
REQ_DB_MAIN.REQUISITION_ID IN (SELECT CASE_ID FROM CaseIDsToProcess )
AND REQ_SUBMITTER_ID IS NOT NULL) REQDBMAINResults
WHERE REQDBMAINResults.RankOrder=1
),

PatientInformation AS (
SELECT
DISTINCT
CASE WHEN NVL(REQ_DB_MAIN.REQ_GROUPER_ID,0) <>0 THEN CAST (NVL(REQ_DB_MAIN.REQ_GROUPER_ID,'0') AS VARCHAR2(25)) ELSE PATIENT.PAT_ID END || '-' ||LAB_CASE_DB_MAIN.CASE_ID uniqueid,
LAB_CASE_DB_MAIN.CASE_ID
FROM CaseIDsToProcess
INNER JOIN CLARITY.LAB_CASE_DB_MAIN ON CaseIDsToProcess.CASE_ID=LAB_CASE_DB_MAIN.CASE_ID
LEFT JOIN CLARITY.PATIENT ON PATIENT.PAT_ID=LAB_CASE_DB_MAIN.CASE_PAT_ID
LEFT JOIN CLARITY.REQ_CASES ON REQ_CASES.CASES_ID = CaseIDsToProcess.CASE_ID
LEFT JOIN CLARITY.REQ_DB_MAIN ON REQ_DB_MAIN.REQUISITION_ID = CLARITY.REQ_CASES.REQUISITION_ID
LEFT JOIN CLARITY.RQG_DB_MAIN ON REQ_DB_MAIN.REQ_GROUPER_ID = RQG_DB_MAIN.RQG_GROUPER_ID
LEFT JOIN CLARITY.SPEC_DB_MAIN ON LAB_CASE_DB_MAIN.CASE_ID=SPEC_DB_MAIN.CASE_ID
LEFT JOIN CLARITY.LAB_SMT_NOADD ON SPEC_DB_MAIN.REQ_SMT_ID = Lab_Smt_Noadd.Record_Id
LEFT JOIN CLARITY.REQ_ORDER_GROUP ON REQ_ORDER_GROUP.REQUISITION_ID=LAB_CASE_DB_MAIN.CASE_ID
LEFT JOIN CLARITY.ORDER_PROC ON REQ_ORDER_GROUP.ORDER_ID=ORDER_PROC.ORDER_PROC_ID
LEFT JOIN ExternalPatientInfo ON REQ_DB_MAIN.REQ_GROUPER_ID = ExternalPatientInfo.REQ_GROUPER_ID
),

PatientCases AS
(
SELECT
DISTINCT
PatientInformation.uniqueid,
LAB_CASE_INFO.CASE_NUM,
LAB_CASE_DB_MAIN.CASE_ID,
LAB_CASE_INFO.AP_CASE_STATUS_C
FROM CaseIDsToProcess
INNER JOIN CLARITY.LAB_CASE_DB_MAIN ON CaseIDsToProcess.CASE_ID=LAB_CASE_DB_MAIN.CASE_ID
INNER JOIN CLARITY.LAB_CASE_INFO ON LAB_CASE_INFO.REQUISITION_ID = LAB_CASE_DB_MAIN.CASE_ID
INNER JOIN CLARITY.ZC_AP_CASE_STATUS ON ZC_AP_CASE_STATUS.AP_CASE_STATUS_C = LAB_CASE_INFO.AP_CASE_STATUS_C
INNER JOIN PatientInformation ON CaseIDsToProcess.CASE_ID=PatientInformation.Case_Id
),

CASERESULT AS (
SELECT
UNIQUEID,
CASE_ID,
CASE_NUM,
RESULT_ID,
AP_CASE_STATUS_C,
RES_VAL_STATUS_C,
Line,
Value_Line,
COMPONENT_ID,
EXTERNAL_NAME,
REPLACE(REPLACE(MULT_LN_VAL_STORAGE,'\\','\\\\'),'"','\\"') AS ResultText,
CMP_MULTILINE_VALUE
FROM
(
SELECT DISTINCT
h.uniqueid,
a.Case_ID,
h.CASE_NUM,
c.RESULT_ID,
d.COMPONENT_ID,
NVL(e.CMP_MULTILINE_VALUE,1) AS CMP_MULTILINE_VALUE,
NVL(d.LINE,1) AS LINE,
NVL(f.VALUE_LINE,1) AS VALUE_LINE ,
g.EXTERNAL_NAME,
NVL(f.MULT_LN_VAL_STORAGE,NVL(d.COMPONENT_RESULT,'')) AS "MULT_LN_VAL_STORAGE",
RANK() OVER ( Partition BY a.CASE_ID Order by c.Result_ID DESC) AS "RANK",
c.RES_VAL_STATUS_C,
h.AP_CASE_STATUS_C
FROM CaseIDsToProcess a
, CLARITY.SPEC_DB_MAIN b
, CLARITY.RES_DB_MAIN c
, CLARITY.Res_Components d
, CLARITY.RES_VAL_PTR_RM e
, CLARITY.RES_VAL_DATA_RM f
, CLARITY.CLARITY_COMPONENT g
, PatientCases h
WHERE
a.CASE_ID=b.CASE_ID
AND a.CASE_ID=h.CASE_ID
AND b.SPECIMEN_ID = c.RES_SPECIMEN_ID
AND c.RESULT_ID = d.RESULT_ID
AND d.LINE = e.GROUP_LINE (+)
AND d.RESULT_ID = e.RESULT_ID (+)
AND e.CMP_MULTILINE_VALUE = f.GROUP_LINE (+)
AND e.RESULT_ID = f.RESULT_ID (+)
AND d.COMPONENT_ID= g.COMPONENT_ID
-- AND c.RES_VAL_STATUS_C = 9
AND d.COMPONENT_REPORT_YN=1
AND d.COMPONENT_ID <> 7100796
) RESULT
WHERE RANK=1
AND MULT_LN_VAL_STORAGE IS NOT NULL
)SELECT UNIQUEID,CASE_ID,CASE_NUM, RESULT_ID,AP_CASE_STATUS_C,RES_VAL_STATUS_C,Line,Value_Line,COMPONENT_ID,EXTERNAL_NAME,ResultText,CMP_MULTILINE_VALUE
FROM CaseResult
where component_id = 7000004 --MICROSCOPIC DESCRIPTION
ORDER BY UNIQUEID, LINE, CMP_MULTILINE_VALUE, VALUE_LINE
;

/**************************************************

If you need to merge the results into one line,

you may use the following pice

**************************************************/

DROP TABLE xdr_prinv_opr_results_merge PURGE;

CREATE TABLE xdr_prinv_opr_results_merge AS
SELECT uniqueid,case_id,RTRIM(XMLAGG(XMLELEMENT(E,resulttext,'').EXTRACT('//text()')
ORDER BY value_line).GetClobVal(),',') as result_text_merged
from xdr_prinv_opr_results
GROUP BY uniqueid,case_id;
```
## Check Patient's Contact Information

###### (by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 10/08/19)

The following code can be used to check if/when a patient has (or not) different forms of contact. The BIP_PAT_GEOCODE table contains patient's geocoded address.

Note: this looks for valid address AND phone AND email.   If you want to pull a patient that has at least one form of valid contact use OR instead of AND in the code below
```sql
SELECT *
FROM xdr_prinv_coh coh
JOIN patient pat ON coh.pat_id = pat.pat_id
left join JSANZ.BIP_PAT_GEOCODE geo on pat.pat_id = geo.pat_id
where

---------------------------------------------------------------------------------

-- excludes patient's without a valid address, or labeled as homeless

---------------------------------------------------------------------------------

geo.add_line_1is not null
and UPPER(geo.add_line_1) NOT like '%HOMELESS%'
and geo.fips is not null
AND -- OR

---------------------------------------------------------------------------------

-- excludes patients with a placeholder/dummy phone # or no phone# at all

-- This can be extended to other phone numbers as well (work phone, cell, etc...)

---------------------------------------------------------------------------------

( pat.home_phone is not null
and pat.home_phone NOT IN ('000-000-0000'
,'000-000-0001'
,'310-000-0000'
,'310-450-1748'
,'999-999-9999'
)
)

---------------------------------------------------------------------------------

-- excludes patients without email address

---------------------------------------------------------------------------------

AND -- OR
pat.email_address is not null

# All Flowsheet Questions on a Template

###### (BY [Robert Follett (Unlicensed)](https://uclabip.atlassian.net/wiki/people/557058:fb90de8e-f6e8-4a11-b471-2992e0b019af?ref=confluence) ON 10/10/19)

The following code pulls the different flowsheet questions on a given template id.  Use this to see what questions are asked on a template.

select
   t.template_id,
   t.template_name,
   t.display_name template_display_name,
   tc.flo_meas_id group_meas_id,
   fgd.disp_name group_meas_name,
   fgd.flo_meas_name group_flo_meas_name,
   fgd2.flo_meas_id flo_meas_id,
   fgd2.flo_meas_name flo_meas_name,
   fgd2.disp_name flo_disp_name
from
ip_flt_data t
left join ip_flt_comps tc on (t.template_id = tc.template_id)
left join ip_flo_gp_data fgd on (tc.flo_meas_id = fgd.flo_meas_id)
left join ip_flo_measuremnts fm on (fgd.flo_meas_id = fm.id)
join ip_flo_gp_data fgd2 on (fm.measurement_id = fgd2.flo_meas_id)
where t.template_id = 281
group by
t.template_id, t.template_name, t.display_name,
tc.line, tc.flo_meas_id, fgd.disp_name, fgd.flo_meas_name,
fgd2.flo_meas_id, fgd2.flo_meas_name, fgd2.flo_dis_name, fgd2.flo_row_name, fgd2.flo_row_name,
fgd2.value_type_name, fgd2.description, fgd2.disp_name

# Past Medical History

###### (BY [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) ON 12/02/19)

The following code pulls the Past Medical History information. This is a report that can be seen in CC, and the layout of the pull replicates this format. It uses the dx description for the diagnosis, as this is what it's shown in CC.

SELECT *
FROM xdr_prinv_coh coh
JOIN patient pat ON coh.pat_id = pat.pat_id
left join JSANZ.BIP_PAT_GEOCODE geo on pat.pat_id = geo.pat_id
where

---------------------------------------------------------------------------------

-- excludes patient's without a valid address, or labeled as homeless

---------------------------------------------------------------------------------

geo.add_line_1is not null
and UPPER(geo.add_line_1) NOT like '%HOMELESS%'
and geo.fips is not null
AND -- OR

---------------------------------------------------------------------------------

-- excludes patients with a placeholder/dummy phone # or no phone# at all

-- This can be extended to other phone numbers as well (work phone, cell, etc...)

---------------------------------------------------------------------------------

( pat.home_phone is not null
and pat.home_phone NOT IN ('000-000-0000'
,'000-000-0001'
,'310-000-0000'
,'310-450-1748'
,'999-999-9999'
)
)

---------------------------------------------------------------------------------

-- excludes patients without email address

---------------------------------------------------------------------------------

AND -- OR
pat.email_address is not null

# All Flowsheet Questions on a Template

###### (BY [Robert Follett (Unlicensed)](https://uclabip.atlassian.net/wiki/people/557058:fb90de8e-f6e8-4a11-b471-2992e0b019af?ref=confluence) ON 10/10/19)

The following code pulls the different flowsheet questions on a given template id.  Use this to see what questions are asked on a template.

select
   t.template_id,
   t.template_name,
   t.display_name template_display_name,
   tc.flo_meas_id group_meas_id,
   fgd.disp_name group_meas_name,
   fgd.flo_meas_name group_flo_meas_name,
   fgd2.flo_meas_id flo_meas_id,
   fgd2.flo_meas_name flo_meas_name,
   fgd2.disp_name flo_disp_name
from
ip_flt_data t
left join ip_flt_comps tc on (t.template_id = tc.template_id)
left join ip_flo_gp_data fgd on (tc.flo_meas_id = fgd.flo_meas_id)
left join ip_flo_measuremnts fm on (fgd.flo_meas_id = fm.id)
join ip_flo_gp_data fgd2 on (fm.measurement_id = fgd2.flo_meas_id)
where t.template_id = 281
group by
t.template_id, t.template_name, t.display_name,
tc.line, tc.flo_meas_id, fgd.disp_name, fgd.flo_meas_name,
fgd2.flo_meas_id, fgd2.flo_meas_name, fgd2.flo_dis_name, fgd2.flo_row_name, fgd2.flo_row_name,
fgd2.value_type_name, fgd2.description, fgd2.disp_name

# Past Medical History

###### (BY [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) ON 12/02/19)

The following code pulls the Past Medical History information. This is a report that can be seen in CC, and the layout of the pull replicates this format. It uses the dx description for the diagnosis, as this is what it's shown in CC.

##### **Past Medical History**

select x.pat_id
    ,x.study_id
    ,x.icd_code
    ,x.dx_name as diagnosis_desc
    ,x.medical_hx_date
    ,x.comments
    from (select distinct coh.pat_id
    ,coh.study_id
    ,case when pl.contact_date <= '01/01/2015' then cin9.code else cin10.code end icd_code
    ,edg.dx_name
    ,pl.contact_date
    ,pl.medical_hx_date
    ,pl.comments
    ,pl.dx_id
    ,MIN(pl.contact_date) OVER (PARTITION BY pl.pat_id, pl.dx_id) AS first_dx
From XDR_PRINV_COH coh
JOIN MEDICAL_HX pl ON coh.pat_id = pl.pat_id
left join ZC_HISTORY_SOURCE zhs on pl.MED_HX_SOURCE_C = zhs.HISTORY_SOURCE_C
--This join helps us find the dx description that matches what can be seen in CC
LEFT JOIN clarity.CLARITY_EDG edg ON pl.dx_id = edg.dx_id --AND cin9.line = 1
-- ICD9 CODES JOIN
LEFT JOIN clarity.edg_current_icd9 cin9 ON pl.dx_id = cin9.dx_id AND cin9.line = 1
--ICD10 CODES JOIN
LEFT JOIN clarity.edg_current_icd10 cin10 ON pl.DX_ID = cin10.dx_id AND cin10.line = 1
) x
where
first_dx = x.contact_date
order by pat_id,diagnosis_desc;
```
## Procedure Performing and Billing Providers

###### (by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 3/2/20)

The following code enhances the procedure data pull to add the performing and billing providers. It only applies to the CPT (not legacy but **hsp_transactions**, and **arpb_transactions**) and ICD-9/10 codes (**i2b2**.**lz_clarity_procedures**)
```sql
-- Procedure Performance Provider**

DROP TABLE xdr_prinv_prc PURGE;

CREATE TABLE xdr_prinv_prc AS
SELECT DISTINCT enc.pat_id
   ,coh.study_id
   ,hspt.pat_enc_csn_id
   ,hspt.service_date AS proc_date
   ,SUBSTR(COALESCE(hspt.hcpcs_code,hspt.cpt_code),1,5) AS proc_code
   ,'CPT-Hospital' AS proc_type
   ,hspt.PERFORMING_PROV_ID
   ,hspt.BILLING_PROV_ID AS BILLING_PROV_ID
   ,EAP.PROC_NAME
FROM i2b2.lz_clarity_enc enc
JOIN xdr_prinv_coh coh ON enc.pat_id = coh.pat_id
JOIN hsp_transactions hspt ON enc.pat_enc_csn_id = hspt.pat_enc_csn_id
LEFT OUTER JOIN f_arhb_inactive_tx fait ON hspt.tx_id = fait.tx_id
LEFT JOIN clarity_eap eap ON SUBSTR(COALESCE(hspt.hcpcs_code,hspt.cpt_code),1,5) = eap.proc_code
WHERE hspt.tx_type_ha_c = 1
AND (LENGTH(hspt.cpt_code) = 5 OR hspt.hcpcs_code IS NOT NULL)
AND fait.tx_id IS NULL;

CREATE INDEX xdr_prinv_prc_patidx ON xdr_prinv_prc (pat_id) nologging;

SELECT COUNT(*),COUNT(DISTINCT pat_id) FROM xdr_prinv_prc; --

SELECT COUNT(*),COUNT(DISTINCT pat_id) FROM xdr_prinv_prc WHERE PERFORMING_PROV_ID IS NOT NULL; --

ALTER TABLE XDR_prinv_PRC MODIFY proc_type varchar2 (200);

INSERT INTO XDR_prinv_PRC (pat_id,STUDY_ID,pat_enc_csn_id,proc_date,proc_code,proc_type,PERFORMING_PROV_ID,PROC_NAME)
SELECT distinct arpb.patient_id AS pat_id
   ,coh.study_id
   ,arpb.pat_enc_csn_id
   ,arpb.service_date AS proc_date
   ,arpb.cpt_code AS proc_code
   ,'CPT-Professional' AS proc_type
   ,arpb.SERV_PROVIDER_ID AS PERFORMING_PROV_ID
   ,arpb.BILLING_PROV_ID AS BILLING_PROV_ID
   ,EAP.PROC_NAME
FROM xdr_prinv_enc enc
JOIN xdr_prinv_coh coh ON enc.pat_id = coh.pat_id
JOIN arpb_transactions arpb ON enc.pat_enc_csn_id = arpb.pat_enc_csn_id
LEFT JOIN clarity_eap eap ON arpb.cpt_code = eap.proc_code
WHERE tx_type_c = 1 ----- Charges only
AND void_date IS NULL;

COMMIT;

SELECT proc_type, COUNT(*),COUNT(DISTINCT pat_id) FROM xdr_prinv_prc GROUP BY proc_type;

/*

CPT-Professional
CPT-Hospital

*/

SELECT * FROM xdr_prinv_prc;

INSERT INTO xdr_prinv_prc (pat_id,STUDY_ID,pat_enc_csn_id,proc_date,proc_code,proc_type,PERFORMING_PROV_ID,PROC_NAME)
SELECT DISTINCT pat.pat_id
   ,coh.study_id
   ,px.pat_enc_csn_id
   ,px.proc_date
   ,px.px_code AS proc_code
   ,CASE
   WHEN px.icd_code_set = 'ICD-9-CM Volume 3' THEN 'ICD-9'
   WHEN px.icd_code_set = 'ICD-10-PCS' THEN 'ICD-10'
   END AS proc_type
   ,px.PROC_PERF_PROV_ID
   ,px.procedure_name AS proc_name
FROM xdr_prinv_coh coh
JOIN i2b2.lz_clarity_procedures px ON pat.pat_enc_csn_id = px.pat_enc_csn_id;

COMMIT;

/*

CPT-Professional
CPT-Hospital
ICD-9
ICD-10

*/

# Database Cleanup/Queries

The following code sets up the drop table queries as well as calculate the current schema sizes.  Update requirements accordingly.

##### **Database Cleanup/Queries**

-------------------------------------------------------------------------------

-- Generate queries to drop tables for cleanup

-------------------------------------------------------------------------------

-- Drop tables based on owners/schemas/table names

select 'drop table ' || owner || '.' || t.table_name || ' PURGE; --' || p.investigator sqlq
from all_tables t
join i2b2.bip_project p on substr(t.table_name,5,6) = to_char(p.project_id)
WHERE t.owner in ('TTACORDA','CTSI_RESEARCH','CKD')
AND ((not regexp_like (table_name,'+(drv|coh|bu|ctl|donotdelete)+','i'))
or (regexp_like (table_name,'+(subset)+','i')))
and t.table_name like 'XDR%'
and (t.owner in ('TTACORDA','CKD')
or (t.owner = 'CTSI_RESEARCH'
and lower(p.developer) in ('ehb','ttacorda','redcap2xdr')
)
)
order by sqlq
;

-- Drop according to own tables sorted by descending size

select distinct 'drop table '
|| x.table_name
|| ' PURGE; --'
|| z.SIZE_MB
|| ' | ' || x.developer
|| ' | ' || x.investigator
|| ' | ' || x.irb
|| ' | ' || x.description
sqlq
,z.SIZE_MB
from (select distinct to_number(substr(t.table_name,5,6)) project_id
,t.owner || '.' || t.table_name table_name
,t.table_name table_name_pre
,p.developer
,p.investigator
,p.irb
,p.description
from all_tables t
join i2b2.bip_project p on to_number(substr(t.table_name,5,6)) = p.project_id
where t.owner = 'CTSI_RESEARCH'
and substr(t.table_name,1,4) = 'XDR_'
and LENGTH(TRIM(TRANSLATE(substr(t.table_name,5,6), '+-.0123456789',' '))) is null
) x
join (SELECT OBJECT_NAME, OBJECT_TYPE, IOT_TYPE, SEGMENT_NAME, (NVL(KB, 0) + NVL(KBIOT, 0)) /1024 AS SIZE_MB
FROM
(
SELECT do.object_name, do.object_type, DT.IOT_TYPE,
case when DT.IOT_TYPE LIKE 'IOT%' THEN
DS_IOT.SEGMENT_NAME
ELSE DS.SEGMENT_NAME
END AS SEGMENT_NAME
, case when DT.IOT_TYPE LIKE 'IOT%' THEN
CONS.INDEX_NAME
ELSE NULL
END AS IOT_INDEX_NAME,
sum(DS.bytes)/1024 KB,
sum( case when DT.IOT_TYPE LIKE 'IOT%' THEN
DS_IOT.bytes/1024
else null end)
as KBIOT
FROM dba_objects do
LEFT OUTER JOIN DBA_segments DS
ON do.object_name = DS.segment_name
AND DO.OWNER = DS.OWNER
LEFT OUTER JOIN DBA_TABLES DT
ON DO.OBJECT_NAME = DT.TABLE_NAME
AND DO.OWNER = DT.OWNER
LEFT OUTER JOIN all_constraints cons
ON CONS.OWNER = DT.OWNER
AND cons.table_name = DT.table_name
AND cons.constraint_type = 'P'
LEFT OUTER JOIN DBA_segments DS_IOT
ON CONS.INDEX_NAME = DS_IOT.segment_name
AND DS_IOT.OWNER = CONS.OWNER
where do.owner = 'CTSI_RESEARCH'
AND do.object_type = 'TABLE'
and do.object_name like 'XDR%'
group by do.object_name, do.object_type, DT.IOT_TYPE,
case when DT.IOT_TYPE LIKE 'IOT%' THEN
DS_IOT.SEGMENT_NAME
ELSE DS.SEGMENT_NAME
END
, case when DT.IOT_TYPE LIKE 'IOT%' THEN
CONS.INDEX_NAME
ELSE NULL
END
)
) z on x.table_name_pre = z.object_name
where x.developer in ('TTACORDA','eHB')
and not regexp_like(x.table_name,'+(coh|drv)+','i')
order by z.SIZE_MB desc --sqlq
;

-------------------------------------------------------------------------------

-- Calculate total allotted database sizes...

-------------------------------------------------------------------------------

-- ...for specific users

SELECT (sum(bytes)/1024/1024)*0.0010 as gb_size
FROM user_segments us
JOIN all_tables t ON us.segment_name = t.table_name
left join i2b2.bip_project p on substr(t.table_name,5,6) = to_char(p.project_id)
WHERE (t.owner in ('TTACORDA','CKD')
or (t.owner = 'CTSI_RESEARCH'
and lower(p.developer) in ('ehb','ttacorda')
and p.project_id is not null
)
)
;

-- ...for specific schemas

SELECT (sum(bytes)/1024/1024)*0.0010 as gb_size --start: 259.60275 end: 244.0289375
FROM user_segments us
JOIN all_tables t ON us.segment_name = t.table_name
WHERE t.owner = 'CTSI_RESEARCH'
;

-- ...for my tables by size

SELECT OBJECT_NAME, OBJECT_TYPE, IOT_TYPE, SEGMENT_NAME, (NVL(KB, 0) + NVL(KBIOT, 0)) /1024 AS SIZE_MB
FROM
(
SELECT do.object_name, do.object_type, DT.IOT_TYPE,
case when DT.IOT_TYPE LIKE 'IOT%' THEN
DS_IOT.SEGMENT_NAME
ELSE DS.SEGMENT_NAME
END AS SEGMENT_NAME
, case when DT.IOT_TYPE LIKE 'IOT%' THEN
CONS.INDEX_NAME
ELSE NULL
END AS IOT_INDEX_NAME,
sum(DS.bytes)/1024 KB,
sum( case when DT.IOT_TYPE LIKE 'IOT%' THEN
DS_IOT.bytes/1024
else null end)
as KBIOT
FROM dba_objects do
LEFT OUTER JOIN DBA_segments DS
ON do.object_name = DS.segment_name
AND DO.OWNER = DS.OWNER
LEFT OUTER JOIN DBA_TABLES DT
ON DO.OBJECT_NAME = DT.TABLE_NAME
AND DO.OWNER = DT.OWNER
LEFT OUTER JOIN all_constraints cons
ON CONS.OWNER = DT.OWNER
AND cons.table_name = DT.table_name
AND cons.constraint_type = 'P'
LEFT OUTER JOIN DBA_segments DS_IOT
ON CONS.INDEX_NAME = DS_IOT.segment_name
AND DS_IOT.OWNER = CONS.OWNER
where do.owner = 'TTACORDA'
AND do.object_type = 'TABLE'
group by do.object_name, do.object_type, DT.IOT_TYPE,
case when DT.IOT_TYPE LIKE 'IOT%' THEN
DS_IOT.SEGMENT_NAME
ELSE DS.SEGMENT_NAME
END
, case when DT.IOT_TYPE LIKE 'IOT%' THEN
CONS.INDEX_NAME
ELSE NULL
END
)
ORDER BY OBJECT_NAME
;

-------------------------------------------------------------------------------

-- Check for numeric values (example)

-------------------------------------------------------------------------------

SELECT case
when regexp_like(ord_value,'+(\.\.)+','i') then ord_value
when LENGTH(TRIM(TRANSLATE(ord_value, '+-.0123456789',' '))) is null then 'value is numeric: ' || ord_value
else ord_value
end value_is_numeric
FROM i2b2.lz_clarity_labs
where rownum <= 1000
;

-------------------------------------------------------------------------------

-- Check code behind views

-------------------------------------------------------------------------------

SELECT VIEW_NAME, TEXT
FROM all_VIEWS
where view_name = 'V_NOTES_ROUTED';

-------------------------------------------------------------------------------

-- Set date formats for output

-------------------------------------------------------------------------------

alter session set nls_date_format = 'MM/DD/YYYY HH24:MI';

-------------------------------------------------------------------------------

-- Analyze tables

-------------------------------------------------------------------------------

ANALYZE TABLE i2b2.bip_project VALIDATE STRUCTURE CASCADE;

-------------------------------------------------------------------------------

-- Check running queries

-------------------------------------------------------------------------------

SELECT distinct s.SID
   ,s.serial#
   ,s.username
   ,s.osuser
   ,program
   ,s.status
   ,s.sql_exec_start
   ,sa.rows_processed --will not change if query is hung up or in waiting status
   ,coalesce(sa.sql_text,sq.sql_text)
   ,s.sql_id
   ,s.inst_id
   ,s.event
   ,s.logon_time
   ,sa.cpu_time
   ,sa.elapsed_time
   ,sa.runtime_mem
FROM gv$session s
LEFT JOIN v$sqlarea sa ON s.sql_id = sa.sql_id
LEFT JOIN gv$sql sq ON s.sql_id = sq.sql_id
WHERE s.username IN ('TTACORDA','CKD','CTSI_RESEARCH')
AND program in ('SQL Developer','sqlplus.exe')
and s.osuser = 'TheonaT1'
ORDER BY program desc, status, username DESC
,sql_exec_start
;

-------------------------------------------------------------------------------

-- Check sessions that have table locks

-------------------------------------------------------------------------------

select a.sid, a.serial #
from v$session a, v$locked_object b, dba_objects c
where b.object_id = c.object_id
and a.sid = b.session_id
and OBJECT_NAME='LZ_CLARITY_PATIENT';

-------------------------------------------------------------------------------

-- Set privileges

-------------------------------------------------------------------------------

-- Revoke

select
'revoke all privileges ON ' || A.OWNER || '.' || A.TABLE_NAME || ' from CTSI_RESEARCH;'

ScriptIt

FROM ALL_TABLES A
where a.owner = 'I2B2'
and lower(a.table_name) like 'lz_clarity%'
order by a.table_name
;

-- Grant

SELECT
'GRANT SELECT, INSERT, UPDATE, DELETE ON ' || A.OWNER || '.' || A.TABLE_NAME || ' TO CTSI_RESEARCH WITH GRANT OPTION;'
ScriptIt
FROM ALL_TABLES A
WHERE A.OWNER = 'I2B2'
order by a.table_name
;

-------------------------------------------------------------------------------

-- Copy from Rob's schema to ctsi_research

-------------------------------------------------------------------------------

select table_name --35 rows found
from all_tables
where owner = 'RFOLLETT'
and length(table_name) > 26
;

select table_name, length(table_name) len --22 rows found, but max len=26
from all_tables
where owner = 'RFOLLETT'
and substr(table_name,1,4) <> 'XDR_'
order by len desc
;

select distinct 'create table ctsi_research.'
|| case
when table_name like 'XDR%' then regexp_replace(table_name,'XDR_','RFX_',1,1)
else table_name || '_RFX'
end
|| ' as select * from rfollett.'
|| table_name
|| ';'

cr
from all_tables
where owner = 'RFOLLETT'
order by cr
;

-- run this, then export to a text file...

select case
when line = 1 then 'create or replace ' || text
else text
end
from all_source
where owner = 'RFOLLETT'
order by name, type, line
;

-------------------------------------------------------------------------------

-- Get a random password

-------------------------------------------------------------------------------

select dbms_random.string('l',1) || dbms_random.string('x',4) ||dbms_random.string('l',1) ||dbms_random.string('x',2) from dual;

-------------------------------------------------------------------------------

-- Get the timestamp for when a table was created

-------------------------------------------------------------------------------

select created from user_objects where lower(object_name)='xdr_123456_demo';
```
## Care Everywhere Labs

###### (by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 9/22/20)

The following code allows you to find labs results received through the care Everywhere interface
there is a practrical application for this for COVID tests in the ETL under
CTSI_RESEARCH.PKG_COVID_ETL_REDCAP.covid_redcap_amb_covid_labs

``` sql
------------------------------------------------

-- Create table with all CE labs documents

------------------------------------------------

CREATE TABLE XDR_prinv_ce_docs as

select DISTINCT COH.PAT_ID
   ,doc.DOC_SOURCE_ORG_ID as organization_id
   ,org.organization_name
   ,DOC.DOCUMENT_ID
   ,info.RESULT_INST_CMP_DTTM
   ,info.RES_PROC_CMP_ID -- this has been empty in my experience
   ,info.RES_PROC_NAME_CMP
   ,info.RESULT_KEY_CMP
   --,eap.PROC_NAME
   --,eap.proc_code
from xdr_prinv_coh coh
JOIN DOCS_RCVD doc ON coh.PAT_ID = doc.PAT_ID
join DOCS_RCVD_RES_INFO info ON doc.DOCUMENT_ID = info.DOCUMENT_ID
join ORG_DETAILS org on doc.DOC_SOURCE_ORG_ID = org.ORGANIZATION_ID
--driver can be used here, or in some of other steps, since you might need to run this first without restrictions and run it by the PI
--join xdr_prinv_CEdrv DRV ON info.RES_PROC_NAME_CMP = drv.RES_PROC_NAME_CMP
-- and org.organization_id= drv.organization_id
--There doesn't seem to be data to match here but just in case
--left join CLARITY_EAP eap ON info.RES_PROC_CMP_ID = eap.PROC_ID --RES_PROC_CMP_ID is null, therefore it can't be match to clarity_EAP
WHERE
--Pull labs only
(doc.TYPE_C = 8 --Lab Results
or
doc.DOC_CONTENT_TYPE_C = 8);
------------------------------------------------
-- Create final table with all CE labs documents
-- by adding the component name and the results, and applying the driver
-- if it hasn't been applied yet
-- (note: when I tried these two steps into one, performance was terrible)
------------------------------------------------

CREATE TABLE XDR_prinv_ce_labs as

select distinct doc.PAT_ID
   ,doc.organization_id
   ,doc.organization_name
   ,DOC.DOCUMENT_ID
   ,doc.RESULT_INST_CMP_DTTM
   --,doc.RES_PROC_CMP_ID -- this has been empty in my experience
   ,doc.RES_PROC_NAME_CMP
   ,doc.RESULT_KEY_CMP
   ,res.RES_COMP_LOINC_CMP -- this has been empty in my experience
   ,res.RES_COMP_NAME_CMP
   ,res.RES_VAL_COMP
from XDR_prinv_ce_docs doc
JOIN DOCS_RCVD_RES_COMP res on doc.document_id = res.document_id
--driver can be used here, or in some of other steps, since you might need to run this first without restrictions and run it by the PI
--join xdr_prinv_CEdrv DRV ON doc.RES_PROC_NAME_CMP = drv.RES_PROC_NAME_CMP
and res.RES_COMP_NAME_CMP = drv.RES_COMP_NAME_CMP
and doc.organization_id = drv.organization_id
where res.RES_COMP_STAT_CMP_C = 4 --4 - completed
;

------------------------------------------------

-- Create driver table for PI

------------------------------------------------

CREATE TABLE xdr_prinv_CEdrv AS
SELECT organization_id
   ,organization_name
   ,RES_PROC_CMP_ID
   ,RES_PROC_NAME_CMP
   ,RES_PROC_CMP_ID
   ,RES_PROC_NAME_CMP
FROM XDR_prinv_ce_docs
,COUNT(*)
GROUP BY organization_id
,organization_name
,RES_PROC_CMP_ID
,RES_PROC_NAME_CMP
,RES_PROC_CMP_ID
,RES_PROC_NAME_CMP
;

------------------------------------------------

-- Final data pull

--It's possible that the results aren't compatible from site to site

-- but this will require further investigation on a case by case basis

------------------------------------------------

SELECT PAT_ID
--,DOCUMENT_ID
   ,organization_name
   ,RESULT_INST_CMP_DTTM AS RESULT_TIME
   ,RES_PROC_NAME_CMP AS PROC_NAME
   ,RES_COMP_NAME_CMP AS COMPONENT_NAME
   ,RES_VAL_COMP AS RESULTS
FROM XDR_prinv_ce_labs lab
--apply driver selection
join xdr_prinv_CEdrv DRV ON lab.RES_PROC_NAME_CMP = drv.RES_PROC_NAME_CMP
and lab.RES_COMP_NAME_CMP = drv.RES_COMP_NAME_CMP
and lab.organization_id = drv.organization_id
;
```
# Occupation Information

###### (by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 10/21/20)

The following code allows you to find a patient's occupation information (patient_4.occupation_c is not currently being used at the time of adding this script here)

```sq;
SELECT DISTINCT pat.pat_id
,pt3.OCCUPATION
FROM xdr_prinv_pat coh
left join PATIENT_3 pt3 ON pat.pat_id = pt4.pat_id
;

# Health Maintenance Topics

###### (by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 12/04/20)

The following code allows you to find information from the Health Maintenance panel for any given patient. This is the [link to Galaxy](https://uclabip.atlassian.net/wiki/spaces/WISE/pages/6455318/JIRA+reports) where to read about the Panel. And this is the link to learn about the table storing the data [\[F_HM_TREND\]](https://datahandbook.epic.com/ClarityDictionary/Details?tblName=F_HM_TREND)

Below is walkthrough of a case to look for Colonoscopy screenings

/*******************************************

Look up the topic name that you need
and use HM_topic_id in the next query
to identify your topic

*******************************************/

select *
from CLARITY_HM_TOPIC;

/*******************************************

Plug the value from the previous step in QUALIFIED_HMT_ID
in the script below to obtain the most recent status for this topic
Modifier: By modifying the join, you can use a given date
and time threshold to look back at previous instances
(the QUALIFIED_HMT_ID below -7770000132-
pulls Colonoscopy preventive screening
status \[HMT_DUE_STATUS_C\]
within 90 days of a date given by the PI \[date_1\])

*******************************************/

SELECT X.pat_id
   ,x.period
   ,x.extract_date
   ,x.IDEAL_RETURN_DT
   ,x.due_status
   FROM (
   select distinct coh.pat_id
   ,'date_1' as period
   --,coh.date_2
   ,hm.extract_date
   ,hm.TOPIC_NAME
   ,hm.IDEAL_RETURN_DT
   ,zst.name as due_status
   ,MAX(hm.extract_date) OVER (PARTITION BY coh.pat_id) AS latest_measure
from xdr_prinv_coh coh
left JOIN F_HM_TREND hm ON coh.pat_id = hm.pat_id --and trunc(hm.extract_date) <= coh.date_1 + 90
left join ZC_HMT_DUE_STATUS zst ON hm.HMT_DUE_STATUS_C = zst.HMT_DUE_STATUS_C
where
hm.QUALIFIED_HMT_ID = 770000132 --Colon Ca Screening: COLONOSCOPY
-- 9 --Advanced Directive
) x
WHERE x.extract_date = latest_measure
;
```
## Questionnaires and Answers

(by [Robert Follett (Unlicensed)](https://uclabip.atlassian.net/wiki/people/557058:fb90de8e-f6e8-4a11-b471-2992e0b019af?ref=confluence) on 02/12/2021)

This code will pull questions and answers from MyChart questionnaires.

```sql
select distinct coh.study_id
   ,coh.study_csn
   --pefa.pat_enc_csn_id
   -- ,pefa.pat_id
   ,pefa.qf_lqf_id as form_id
   -- ,pefa.qf_hqa_id as answer_id
   -- ,qf.form_name
   ,qf.pat_frndly_name
   -- ,cqa.quest_id
   ,clqo.question
   ,cqa.line as ans_line
   ,quest_answer
   ,question_instant as answer_time
from pat_enc_form_ans pefa
---- Can join on csn or pat_id
join YOUR_COHORT coh on pefa.pat_enc_csn_id = coh.pat_enc_csn_id
-- join YOUR_COHORT coh on pefa.pat_id = coh.pat_id
join cl_qform qf on pefa.qf_lqf_id = qf.form_id
join cl_qanswer_qa cqa on pefa.qf_hqa_id = cqa.answer_id
--join cl_qquest cqq on cqa.quest_id = cqq.quest_id -- question name
join cl_qquest_ovtm clqo on cqa.quest_id = clqo.quest_id -- question
where pefa.qf_lqf_id not in (78002) --- Insurance qestionnaire
order by coh.study_id, pefa.qf_lqf_id, cqa.line;

# Address History (includes homeless)

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 3/25/2021)

This code will obtain address history per patient encounter. Flags are set if the patient is currently homeless or homeless at the visit.

select distinct enc.pat_id
   ,enc.pat_enc_csn_id
   ,enc.effective_date_dt
   ,pat.add_line_1
   ,case
   when pat2.pat_id is not null
   then 1
   else 0
   end homeless_currently
   ,pe4.pat_homeless_yn
   ,pahx.eff_start_date
   ,pahx.eff_end_date
   ,pahx.addr_hx_line1
   ,case
   when pe4.pat_enc_csn_id is not null
   or pahx2.pat_id is not null
   then 1
   else 0
   end homeless_at_visit
from i2b2.lz_clarity_enc enc
left join patient pat on enc.pat_id = pat.pat_id
left join pat_addr_chng_hx pahx on enc.pat_id = pahx.pat_id
and enc.effective_date_dt between pahx.eff_start_date
and pahx.eff_end_date
left join patient pat2 on pat.pat_id = pat2.pat_id
and ((pat.add_line_1 = pat2.add_line_1
and regexp_like(pat2.add_line_1,'+(homeless)+','i')
)
or
(pat.city= pat2.city
and regexp_like(pat2.city,'+(homeless)+','i')
)
)
left join pat_enc_4 pe4 on enc.pat_enc_csn_id = pe4.pat_enc_csn_id
and (nvl(pe4.pat_homeless_yn,'N') <> 'N')
left join pat_addr_chng_hx pahx2 on pahx.pat_id = pahx2.pat_id
and pahx.addr_hx_line1 = pahx2.addr_hx_line1
and pahx.city_hx = pahx2.city_hx
and pahx.eff_start_date = pahx2.eff_start_date
and pahx.eff_end_date = pahx2.eff_end_date
and (regexp_like(pahx2.addr_hx_line1,'+(homeless)+','i')
or regexp_like(pahx2.city_hx,'+(homeless)+','i')
)
order by enc.pat_id, enc.effective_date_dt
;

# Cause of Death

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 4/5/2021)

This code will obtain patient cause of death.

--Cause of Death during an admission

select distinct adm.pat_id,
   adm.pat_enc_csn_id,
   dpl10.code as icd_code,
   dpl10.icd_desc
from i2b2.lz_clarity_enc adm --or your cohort
join patient_3 p3 on adm.pat_id = p3.pat_id --(left join if entire cohort is needed)
left join edg_current_icd10 cit on p3.pcod_cause_dx_id = cit.dx_id
and cit.line = 1
left join i2b2.lz_dx_px_lookup dpl10 on cit.code = dpl10.code
and dpl10.icd_type = 10
and dpl10.code_type = 'DX'
WHERE trunc(p3.PCOD_INST_REC_DTTM) between trunc(adm.hosp_admsn_time)
and trunc(adm.hosp_dischrg_time);
--Cause of Death for transplant donors: see Github Code Library (https://github.com/ctsidev/bipdata/blob/master/CodeLibrary/Transplant.sql)

# GFR labs

(by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 6/16/2021)

Code to calculate GFR results taking into account race. You will have to left join to patient table and lookup patient_race_c (2 = Black Afircan American)

where
(lab.component_id not in (2452, 11539, 3000375, 10000661, 10010349, 2453, 11540, 3000376, 10000660, 10010348)
OR (lab.component_id in (2452, 11539, 3000375, 10000661, 10010349) and race.patient_race_c <> 2)
OR (lab.component_id in (2453, 11540, 3000376, 10000660, 10010348) and race.patient_race_c = 2)

# Kill Sessions

(by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 6/23/2021)

Code to find SQL Developer sessions open based on user’s schema, and statement to kill that session (this needs to be run by the DBA, but we can help them by providing this information).

select INST_ID,SID,SERIAL#,USERNAME,STATUS,sql_exec_start,LOGON_TIME
from gv$session
where username='JSANZ'
and program = 'SQL Developer'
;

alter system kill session 'SID,SERIAL#';

alter system kill session '578, 13143';

alter system kill session '827, 24565';

# Locked Objects

(by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 6/23/2021)

Code to find lock objects and even generate statement to kill the session that might be keeping it locked.

SELECT SESSION_ID FROM DBA_DML_LOCKS WHERE NAME = 'OBSERVATION_FACT';
SELECT SID, SERIAL# FROM V$SESSION WHERE SID IN (
SELECT SESSION_ID FROM DBA_DML_LOCKS WHERE NAME = 'OBSERVATION_FACT'

);

ALTER SYSTEM KILL SESSION '3669, 14655';

SELECT a.session_id,
   a.oracle_username,
   a.os_user_name,
   b.owner,
   b.object_name,
   b.object_type,
   DECODE(a.locked_mode, 0, '0 - NONE: lock requested but not yet obtained',
   1, '1 - NULL',
   2, '2 - ROWS_S (SS): Row Share Lock',
   3, '3 - ROW_X (SX): Row Exclusive Table Lock',
   4, '4 - SHARE (S): Share Table Lock',
   5, '5 - S/ROW-X (SSX): Share Row Exclusive Table Lock',
   6, '6 - Exclusive (X): Exclusive Table Lock') LOCKED_MODE
FROM v$locked_object a,
dba_objects b
Where A.Object_Id = B.Object_Id
and a.os_user_name = 'JavierS1';
```
## Recruitment Request Prioritization

(by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 7/16/2021)

```sql
Criteria to narrow down patient selection to meet disclousure criteria (count).

/*******************************************************************************************

1. patients have the contact information field(s) completed
2. patients had a completed UCLA encounter in the last 12 months
3. After this, we will apply a randomization function and keep track of what patients have been sent to PI team already (and which ones are left to send)

Once PI team goes through the list and request more, we will send another release (ex 499).

*******************************************************************************************/

create table XDR_PRINV_COH_recruit_release as

SELECT distinct coh.ip_patient_id
   ,coh.pat_fist_name
   ,coh.pat_last_name
   ,coh.phone
   ,coh.addr_line_1
   ,coh.email
FROM XDR_PRINV_COH coh
left join i2b2.lz_clarity_enc enc on enc.pat_id = coh.pat_id
-- UCLA encounter completed in the last 12 months
and enc.effective_date_dt between current_time - 365.25 and current_time
and ((enc.enc_type_c <> '5' --TT-4/11/2016: Exclude Canceled appointments
OR enc.appt_status_c is null or enc.appt_status_c = 2)
WHERE
-- contant information available
(PHONE IS NOT NULL
ADDR_LINE1 IS NOT NULL
AND EMAIL IS NOT NULL
)
and enc.pat_id is not null
;

randomization (pending)
```
## Bone Marrow Transplant (BMT)

(by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 8/31/2021)

```sql
Code to find the BMT records.

select distinct coh.pat_id
   ,ep.START_DATE as tx_date
   ,'Bone marrow' as tx_name
from xdr_prinv_coh coh
join pat_enc enc on coh.pat_id = enc.pat_id and coh.deleted_yn is null
join Episode_Link lnk on enc.pat_enc_csn_id = lnk.pat_enc_csn_id
join Episode ep on lnk.episode_id = ep.episode_id
left join BMT_INFO bmt on ep.episode_id = bmt.SUMMARY_BLOCK_ID
where --64 cases
lnk.SUM_BLK_TYPE_ID = 2050001100 ----2050001100 "HSCT ALLOGENEIC RECIPIENT"

```

## Pregnancy/Delivery

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 9/10/2021)

```sql
Code to find pregnancy data.

--------------------------------------------------------------------------------

-- This query results in all pregnancies per patient.
-- To check for "current" pregnancies, see where clause.

--------------------------------------------------------------------------------
--------------------------------------------------------------------------------
-- Get dxs
--------------------------------------------------------------------------------
drop table xdr_123456_cohpregdx purge;
create table xdr_123456_cohpregdx as
select distinct coh.pat_id
               ,dx.contact_date dx_diagnosis_date
               ,dx2.effective_date dx2_diagnosis_date
               ,round(months_between(sysdate, dx.contact_date),2) dx_mos
               ,round(months_between(sysdate, dx2.effective_date),2) dx2_mos
               ,trunc(months_between(dx.contact_date,coh.birth_date)/12) dx_age
               ,trunc(months_between(dx2.effective_date,coh.birth_date)/12) dx2_age
  from xdr_prinv_coh                      coh
  left join i2b2.lz_clarity_diagnosis_new dx   on coh.pat_id = dx.pat_id
                                                  and ((dx.icd_type = 9   --no need to check icd9 for current pregnancy
                                                        and  trunc(dx.contact_date) between to_date('01/01/2013','mm/dd/yyyy')
                                                                                        and to_date('09/30/2015','mm/dd/yyyy')
                                                        and dx.icd_code between '630' and '679.9999999999'
                                                       )
                                                       or 
                                                       (dx.icd_type = 10 
                                                        and trunc(dx.contact_date) >= to_date('10/01/2015','mm/dd/yyyy')
                                                        and regexp_like(dx.icd_code,'^(O|Z3(4|5|A|7))','i')
                                                       )
                                                      )
  left join i2b2.int_dx                   dx2  on coh.pat_id = dx2.pat_id 
                                                  and trunc(dx2.effective_date) between to_date('01/01/2013','mm/dd/yyyy')
                                                                                    and to_date('09/30/2015','mm/dd/yyyy')
                                                  and dx2.icd9_code between '630' and '679.9999999999'
  where dx.pat_id is not null
     or dx2.pat_id is not null
;
--------------------------------------------------------------------------------
-- Get labs
--------------------------------------------------------------------------------
select distinct l.proc_code, l.description, l.component_id, l.component_name
  from i2b2.lz_clarity_labs l
  join i2b2.xdr_covid_preg_lab_drv d on l.component_id = d.component_id
  where l.proc_code not in ('HIS2257','LAB144','HIS5747','LAB6256','HIS2256','HIS2258','POC1004','HIS2259','LAB437','OSL437','POC7')
;
--LAB3227	HCG,TOTAL,QL W/REFL TO QN	10010444	HCG, TOTAL, QL(QST)
--  An hCG level above 25 milli-international units per milliliter (mIU/mL) usually means you're pregnant. But it's not a guarantee. 
--  Your provider may recommend checking your hCG level again in a few days to see if the level rises, which would confirm a pregnancy.
--  Conclusion: ok to exclude these from the list of proc_codes listed above.
drop table xdr_123456_cohpreglab purge;
create table xdr_123456_cohpreglab as
select distinct coh.pat_id
               ,lab.specimn_taken_time 
               ,round(months_between(sysdate, lab.specimn_taken_time),2) lab_mos
               ,trunc(months_between(lab.specimn_taken_time,coh.birth_date)/12) lab_age
  from xdr_prinv_coh                 coh
  join i2b2.bip_encounter_pat_link   enc  on coh.pat_id = enc.pat_id
  join i2b2.lz_clarity_labs          lab  on enc.pat_enc_csn_id = lab.pat_enc_csn_id
                                             and upper(lab.ord_value) = 'POSITIVE' 
                                             and lab.proc_code in ('HIS2257','LAB144','HIS5747','LAB6256','HIS2256','HIS2258','POC1004','HIS2259','LAB437','OSL437','POC7')
  join i2b2.xdr_covid_preg_lab_drv   drv  on lab.component_id = drv.component_id -- driver being used by Covid registry ETL
;
--------------------------------------------------------------------------------
-- Get OB
--------------------------------------------------------------------------------
drop table xdr_123456_cohpregob purge;
create table xdr_123456_cohpregob as
select distinct coh.pat_id
               ,prge.pat_enc_csn_id preg_pat_enc_csn_id
               ,prge.effective_date_dt preg_effective_date_dt
               ,round(months_between(sysdate, prge.effective_date_dt),2) preg_mos
               ,ob.mom_id mom_pat_id
               ,ob.del_dttm delivery_date
               ,max(ob.del_dttm) over (partition by coh.pat_id) delivery_date_last
               ,round(months_between(sysdate, ob.del_dttm),2) ob_mos
               ,ob.ga gestational_age
               ,ob.delmeth_c delivery_method_c
               ,zdt.name delivery_method
  from xdr_prinv_coh         coh
  join v_ob_enc_obgyn_stat   preg on coh.pat_id = preg.pat_id 
                                     and preg.obgyn_stat_c = 4 --pregnant at some point - for "current" pregnancies, see where clause below
  join i2b2.lz_clarity_enc   prge on preg.pat_enc_csn_id = prge.pat_enc_csn_id 
  left join v_ob_del_records ob   on coh.pat_id = ob.mom_id         
  left join zc_delivery_type zdt  on ob.delmeth_c = zdt.delivery_type_c
;
--------------------------------------------------------------------------------
-- Get final pregnancy cohort
--------------------------------------------------------------------------------
drop table xdr_123456_cohpregfin purge;
create table xdr_123456_cohpregfin as
select distinct coh.*
               ,case when dx.pat_id is not null then 1 else 0 end dx
               ,case when lab.pat_id is not null then 1 else 0 end lab
               ,case when ob.pat_id is not null then 1 else 0 end ob
  from xdr_prinv_coh              coh
  left join xdr_123456_cohpregdx  dx    on coh.pat_id = dx.pat_id
  left join xdr_123456_cohpreglab lab   on coh.pat_id = lab.pat_id
  left join xdr_123456_cohpregob  ob    on coh.pat_id = ob.pat_id
  where (dx.pat_id is not null
         or lab.pat_id is not null
         or ob.pat_id is not null
        )
--the following where clause is for "current" pregnancies
--    and (dx.dx_mos <= 9          --qualified for current pregnancy using dx codes (no need to check dx2 because icd-9 codes are before 10/1/2015)
--         or lab.lab_mos <= 9     --qualified for current pregnancy using lab results
--         or ob.preg_mos <= 9     --qualified for current pregnancy using ob data
--        )
--    and (ob.ob_mos is null       --if currently pregnant, has not delivered in the past 9 months
--         or ob.ob_mos > 9        --older deliveries
--        )
;

--------------------------------------------------------------------------------

-- Notes:

-- 1. This query obtains pregnancies based on encounters

-- 2. Change the date criterion of 1/1/2013 per project.

--------------------------------------------------------------------------------

select dx.pat_enc_csn_id
from i2b2.lz_clarity_diagnosis_new dx
where (dx.icd_type = 9
and trunc(dx.contact_date) between to_date('01/01/2013','mm/dd/yyyy')
and to_date('09/30/2015','mm/dd/yyyy')
and dx.icd_code between '630' and '679.9999999999'
)
or (dx.icd_type = 10
and trunc(dx.contact_date) >= to_date('10/01/2015','mm/dd/yyyy')
and regexp_like(dx.icd_code,'^(O|Z3(4|A|7))','i')
)
union
select dx.pat_enc_csn_id
from i2b2.int_dx dx
where trunc(dx.effective_date) between to_date('01/01/2013','mm/dd/yyyy')
and to_date('09/30/2015','mm/dd/yyyy')
and dx.icd9_code between '630' and '679.9999999999'
union
select lab.pat_enc_csn_id
from i2b2.bip_encounter_pat_link enc
join i2b2.lz_clarity_labs lab on enc.pat_enc_csn_id = enc.pat_enc_csn_id
-- driver being used by Covid registry ETL
join i2b2.xdr_covid_preg_lab_drv drv on lab.component_id = drv.component_id
where upper(lab.ord_value) = 'POSITIVE'
and lab.specimn_taken_time >= to_date('01/01/2013','mm/dd/yyyy')
and lab.proc_code in ('HIS2257','LAB144','HIS5747','LAB6256','HIS2256','HIS2258','POC1004','HIS2259','LAB437','OSL437','POC7')
union
select enc.pat_enc_csn_id
from i2b2.lz_clarity_enc enc
join clarity.v_ob_enc_obgyn_stat preg on enc.pat_enc_csn_id = preg.pat_enc_csn_id
where enc.effective_date_dt >= to_date('01/01/2013','mm/dd/yyyy')
and preg.obgyn_stat_c = 4 --pregnant
;

--------------------------------------------------------------------------------

-- Notes:

-- 1. This query obtains specific labs during a pregnancy

-- 2. ep.ob_wrk_edd_dt is the estimated due date

--------------------------------------------------------------------------------

from xdr_123456_lab lab --lab cohort containing pat_id
join i2b2.lz_clarity_enc e on lab.pat_id = e.pat_id
join episode_link el on e.pat_enc_csn_id = el.pat_enc_csn_id
join episode ep on el.episode_id = ep.episode_id
and lab.specimn_taken_time between ep.ob_wrk_edd_dt - 280 and ep.ob_wrk_edd_dt --280=40 pregnancy weeks * 7

```
## Calculate ICU stay times

###### (by @jsanz on 9/29/19)

This code will create a table that will calculate the start and end date/times of each ICU that happened during each encounter. If the patient went in and out of the ICU multiple times during the same encounter. Then the record will show multiple entries for that encounter, one per each episode. In order to calculate the aggregated total time that a patient spent in the ICU during a particular encounter, you can calculate the time from start to end, for each individual stay, and add them by encounter. (this last calculation is not in the code below).
```sql
--------------------------------------

-- create ADT table:
-- this code is slightly different from the LZ ADT table
-- becuase it brings in some additional variables that we use
-- to identify ICU stays. Which can be detemrined by the department name
-- or by the level of care provided to the patient

----------------------------------------

CREATE TABLE xdr_prinv_adt AS
select distinct adt.pat_id,
   adt.pat_enc_csn_id,
   case when adt.event_type_c = 1 and adt.next_out_event_id = enc2.hsp_dis_event_id then 'Admit/Discharge'
   when adt.event_type_c = 1 then 'Admit'
   when adt.event_type_c = 3 and adt.next_out_event_id = enc2.hsp_dis_event_id then 'Discharge'
   else 'Transfer' end as event_type,
   adt.event_id as in_event_id,
   adt.department_id,
   dept.department_name,
   dept.dept_abbreviation,
   dept.specialty,
   dept.rev_loc_id,
   loc.loc_name,
   adt.effective_time as time_in,
   adtout.effective_time as time_out,
   adt.next_out_event_id as out_event_id,
   enc2.hsp_adm_event_id as adm_event_id,
   enc2.hsp_dis_event_id as dis_event_id,
   enc.inpatient_data_id,
   adt.PAT_LVL_OF_CARE_C,
   adt.ACCOMMODATION_C
from clarity_adt adt
join xdr_PRINV_coh_final pat on adt.pat_id = pat.pat_id
join pat_enc enc on adt.pat_enc_csn_id = enc.pat_enc_csn_id
join pat_enc_hsp_2 enc2 on adt.pat_enc_csn_id = enc2.pat_enc_csn_id
left join clarity_adt adtout on adt.next_out_event_id = adtout.event_id
left join clarity_dep dept on adt.department_id = dept.department_id
left join clarity_loc loc on dept.rev_loc_id = loc.loc_id
where adt.event_type_c in (1,3)
and adt.event_subtype_c != 2
and enc2.hsp_adm_event_id is not null;

xdr_covid_icu_times
--
--------------------------------------

-- this procedure runs through the ADT table and calculate
-- the start and end of each ICU that happened during each encounter
-- if the patient went in and out of the ICU multiple times, then the
-- record will show multiple entries for that encounter, with each episode

----------------------------------------

-- create this table to store the output

create table xdr_prinv_icu_times
( "PAT_ID" VARCHAR2(18 BYTE) COLLATE "USING_NLS_COMP",
"CSN" NUMBER(18,0) NOT NULL ENABLE,
"ICU_START" DATE,
"ICU_END" DATE
);

I think that this code should run

--procedure covid_icu as

v_procname varchar2(120) := 'covid_charlson';
begin
i2b2.do_the_truncate('i2b2.xdr_covid_icu_times');
DECLARE
cursor c_icu is
select adt.pat_id
,adt.pat_enc_csn_id
,adt.time_in as icu_in
,adt.time_out as icu_out
from xdr_prinv_adt adt
left join ZC_LVL_OF_CARE zloc on adt.PAT_LVL_OF_CARE_C = zloc.LEVEL_OF_CARE_C
left join ZC_ACCOMMODATION za on adt.ACCOMMODATION_C = za.ACCOMMODATION_C
where department_name like '%ICU%'
OR ( adt.PAT_LVL_OF_CARE_C IS NOT NULL AND adt.PAT_LVL_OF_CARE_C = '6') -- ICU
OR adt.ACCOMMODATION_C IN (25,28,46)
order by adm.pat_id, adm.csn, adt.time_in;
t_rec c_icu%rowtype;
hold_enc number := 0;
hold_pat varchar2(20);
hold_start_time date;
hold_end_time date;

BEGIN
OPEN c_icu;
LOOP
FETCH c_icu into t_rec;
EXIT WHEN c_icu%notfound;
IF hold_enc = 0 THEN -- first pass
hold_enc := t_rec.csn;
hold_pat := t_rec.pat_id;
hold_start_time := t_rec.icu_in;
hold_end_time := t_rec.icu_out;
ELSIF hold_enc = t_rec.csn THEN -- same encounter
IF t_rec.icu_in = hold_end_time THEN --- if icu in = the last out time move the new out time to hold
hold_end_time := t_rec.icu_out;
else -- icu in != icu hold write a record
insert into xdr_prinv_icu_times(pat_id, csn, icu_start, icu_end)
VALUES (hold_pat, hold_enc, hold_start_time, hold_end_time);
hold_start_time := t_rec.icu_in;
hold_end_time := t_rec.icu_out;
END IF;
-- hold_enc != new enc, we have a new telemetry action -- write the old one
ELSE
insert into xdr_prinv_icu_times(pat_id, csn, icu_start, icu_end)
VALUES (hold_pat, hold_enc, hold_start_time, hold_end_time);
hold_enc := t_rec.csn;
hold_pat := t_rec.pat_id;
hold_start_time := t_rec.icu_in;
hold_end_time := t_rec.icu_out;
END IF;
END LOOP;

-- process last record and calculate

insert into xdr_prinv_icu_times(pat_id, csn, icu_start, icu_end)
VALUES (hold_pat, hold_enc, hold_start_time, hold_end_time);
CLOSE c_icu;
END;

commit;

-- end covid_icu;
```
## Glasgow Comma Score (GCS)

###### (by @jsanz on 9/29/19)

The **Glasgow** Coma Scale/**Score** (GCS) estimates coma severity based on Eye (4), Verbal (5), and Motor (6) criteria.. The codes below will help you locate the GCS in your flowsheets table. APply them to the second table that you build.
```sql
| flo_meas_id | flo_meas_name |
| --- | --- |
| 16011292 | Glasgow Coma Scale Score |
| 160348 | Glasgow Coma Scale Score |
| 160351 | Glasgow Coma Scale Score |
| 3040101407 | Glasgow Coma: Age |
| 3040401001 | Glasgow Coma Scale Score |
| 401001 | Glasgow Coma Scale Score |
```
## Sequential Organ Failure Assessment (**SOFA**)

###### (by @jsanz on 9/29/19)

The Sequential Organ Failure Assessment (**SOFA**) Score is a mortality prediction score that is based on the degree of dysfunction of six organ systems. The score is calculated on admission and every 24 hours until discharge using the worst parameters measured during the prior 24 hours.  The code below will pull the SOFA score from your flowsheets table. Apply them to the second table that you build.
```sql
| FLT_ID | TEMPLATE_NAME |
| --- | --- |
| 3040100960 | IP SOFA UCLA |

# DENSE RANK in Oracle

by [Cenan Pirani](https://uclabip.atlassian.net/wiki/people/5f5922bcff2fe2006fb700d8?ref=confluence) on 10/4/2021

### How to pull first value in a query based on order rank

In the example below of LABS we are looking for the most recent labs available per patient. Since labs can be spread out over many dates DENSE RANK allows you to GROUP by patient and component, and then select the first value if the ‘order_time’ is sorted descending per grouped ‘pat_id’.

Example:

select pat_id
,component_name
,max(ord_value) keep (dense_rank first order by order_time desc) as lab_value
,max(reference_unit) keep (dense_rank first order by order_time desc) as lab_value_unit
from xdr_100001_lab
group by pat_id
,component_name
order by pat_id
,component_name
;
```
## Pathology HonestBroker Accession Numbers

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 10/19/2021)

Code to process accession numbers for pathology. This will deidentify accession numbers as well as identify result texts that contain the literals “block” or “cassette”.
```sql
----------------------------------------------------------------------------------------------------------------------

-- After creating pathology (entity_cd=PATH) and pathology_results (entity_cd=PATHR) files, complete the following:

----------------------------------------------------------------------------------------------------------------------

--------------------------------------------------------------------------------

-- Create a new pathology file to encrypt pathology accession number

--------------------------------------------------------------------------------

-- Create a table with deidentified accession numbers

drop table xdr_123456_path_accnum purge;

create table xdr_123456_path_accnum as
select distinct demoi.mrn
,min(i2b2.f_get_deid_new(6,path.accession_number,113164)) over (partition by demoi.study_id, path.accession_number) ip_accession_number
,path.*
from xdr_123456_path path
join xdr_123456_demoi demoi on path.study_id = demoi.study_id
;

-- Ensure this table's counts are identical to the original

select count(*) from xdr_123456_path_accnum;

select count(*) from xdr_123456_path;

-- Ensure the following counts are identical to verify uniqueness

select count(distinct accession_number), count(distinct ip_accession_number) from xdr_123456_path_accnum;

--------------------------------------------------------------------------------

-- Add a column to the pathology results to identify if a result text line
-- contains "block" or "cassette" and update

--------------------------------------------------------------------------------

alter table xdr_123456_pathr add block_cassette_in_txt varchar(1);

update xdr_123456_pathr
set block_cassette_in_txt = 'Y'
where regexp_like(result_txt, 'cassette|block', 'i')
;

commit;

--------------------------------------------------------------------------------

-- Respool pathology files
-- Example of respool:
-- Refer to E:\\Projects\\108564\\Execute_Custom.bat and PullMain_Custom.sql

--------------------------------------------------------------------------------

--spool e:\\projects\\123456\\Data\\Pathology.txt

select 'ip_patient_id,ip_enc_id,ip_order_proc_id,ip_accession_number,order_time,result_time,procedure_description,specimen_source,specimen_type,specimen_taken_time' export from dual union all
select '"' || study_id || '"' || ',' || '"' || study_csn || '"' || ',' || '"' || study_order_proc_id || '"' || ',' || '"' || ip_accession_number || '"' || ',' || '"' || order_time
|| '"' || ',' || '"' || result_time || '"' || ',' || '"' || procedure_description || '"' || ',' || '"' || specimen_source || '"' || ',' || '"' || specimen_type
|| '"' || ',' || '"' || specimen_taken_time || '"' export
from (select distinct study_id, study_csn, study_order_proc_id, ip_accession_number, order_time, result_time, procedure_description, specimen_source, specimen_type
, specimen_taken_time
from xdr_123456_path_accnum
order by study_id, study_order_proc_id
);

--spool off

--spool e:\\projects\\123456\\Data\\Pathology_Results.txt

select 'ip_patient_id,ip_order_proc_id,component_id,component_name,sort_key,block_cassette_in_txt,result_txt' export from dual union all
select '"' || study_id || '"' || ',' || '"' || study_order_proc_id || '"' || ',' || '"' || component_id || '"' || ',' || '"' || component_name
|| '"' || ',' || '"' || sort_key || '"' || ',' || '"' || block_cassette_in_txt || '"' || ',' || '"' || result_txt || '"' export
from (select distinct study_id, study_order_proc_id, component_id, component_name, sort_key, block_cassette_in_txt, result_txt
from xdr_123456_pathr
order by study_id, study_order_proc_id, component_id, sort_key
);

--spool off

--------------------------------------------------------------------------------

-- Do not output this to Data subfolder because this is sent to Clara Magyar, not the PI

--------------------------------------------------------------------------------

--spool e:\\projects\\123456\\Pathology_Cases.txt

select 'ip_patient_id,mrn,case_number,ip_case_number' export from dual union all
select '"' || study_id || '"' || ',' || '"' || mrn || '"' || ',' || '"' || accession_number || '"' || ',' || '"' || ip_accession_number || '"' export
from (select distinct study_id, mrn, accession_number, ip_accession_number
from xdr_123456_path_accnum
where accession_number is not null
order by mrn, accession_number
);

--spool off

--------------------------------------------------------------------------------

-- Rerun Data Dictionary (See E:\\Projects\\123456\\Execute_Custom.bat)

--------------------------------------------------------------------------------

# Imaging HonestBroker Accession Numbers

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 11/5/2021)

Code to process accession numbers for imaging. This will deidentify accession numbers as well as provide modality and list_order_number (sequential number starting with 1).

----------------------------------------------------------------------------------------------------------------------

-- After creating imaging (entity_cd=IMG) files, complete the following:

----------------------------------------------------------------------------------------------------------------------

--------------------------------------------------------------------------------

-- Create a new imaging file to encrypt imaging accession number

-- and obtain other requested variables

--------------------------------------------------------------------------------

-- Create a table with deidentified accession numbers

drop table xdr_123456_img_accnum purge;

create table xdr_123456_img_accnum as
select distinct rownum list_order_number
,demoi.pat_mrn_id mrn
,i2b2.f_get_deid_new(5,img.accession_number,123456) ip_accession_number
,substr(img.procedure_name,1,instr(procedure_name,' ') - 1) modality --get modality up to first space
,img.*
from xdr_123456_img img
join xdr_123456_coh_patonly cpo on img.study_id = cpo.study_id
join i2b2.lz_clarity_patient demoi on cpo.pat_id = demoi.pat_id
;

-- Ensure this table's counts are identical to the original

select count(*) from xdr_123456_img_accnum;

select count(*) from xdr_123456_img;

-- Ensure the following counts are identical to verify uniqueness

select count(distinct accession_number), count(distinct ip_accession_number) from xdr_123456_img_accnum;

--------------------------------------------------------------------------------

-- Respool imaging files

-- Example of respool:

-- Refer to E:\\Projects\\108784\\Execute_Custom.bat and PullMain_Custom.sql

--------------------------------------------------------------------------------

--spool e:\\projects\\123456\\Data\\Imaging.txt

select 'ip_patient_id,ip_enc_id,ip_order_proc_id,procedure_id,procedure_name,ip_accession_number,modality' export from dual union all
select '"' || study_id || '"' || ',' || '"' || study_csn || '"' || ',' || '"' || study_order_proc_id
|| '"' || ',' || '"' || procedure_id || '"' || ',' || '"' || procedure_name
|| '"' || ',' || '"' || ip_accession_number || '"' || ',' || '"' || modality || '"' export
from (select distinct study_id, study_csn, study_order_proc_id, procedure_id
, procedure_name, ip_accession_number, modality
from xdr_123456_img_accnum
order by study_id, study_order_proc_id);

--spool off

--------------------------------------------------------------------------------

-- Do not output this to Data subfolder because this is sent to DICOM, not the PI
-- Spool e:\\projects\\123456\\Imaging_UleadFolderName.txt (e.g. Imaging_Jamshidi_21_10-001869.txt)
-- Copy to Box folder Imaging_Accession_Numbers

--------------------------------------------------------------------------------

--spool E:\\Projects\\123456\\Sent_Backup\\Imaging_UleadFolderName.txt

select 'list_order_number,mrn,ip_patient_id,accession_number,ip_accession_number,modality' export from dual union all
select '"' || list_order_number || '"' || ',' || '"' || mrn || '"' || ',' || '"' || study_id
|| '"' || ',' || '"' || accession_number || '"' || ',' || '"' || ip_accession_number
|| '"' || ',' || '"' || modality
|| '"' export
from (select distinct *
from xdr_123456_img_accnum
where accession_number is not null
and ((LENGTH(TRIM(TRANSLATE(accession_number, '+-.0123456789',' '))) is null --numeric accession_number
and accession_number like '4%'
and length(accession_number) = 8
)
or
modality = 'ECG'
)
order by list_order_number
);

--spool off

--------------------------------------------------------------------------------

-- Rerun Data Dictionary (See E:\\Projects\\108784\\Execute_Custom.bat)

--------------------------------------------------------------------------------

# Changing IDs and MRNs

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 11/5/2021)

Code to process changing IDs and MRNs. Please refer to ctsi_research.f_get_curr_pt. Input variables are pi_type and pi_pat and Output variable is the status and new value. Please see below.

--------------------------------------------------------------------------------

-- Please refer to ctsi_research.f_get_curr_pt(sample of Input/output
-- parameters below and note necessary requirements in comments)

--------------------------------------------------------------------------------

create or replace function f_get_curr_pt
(pi_type in integer --1=pat_id, 2=mrn
,pi_pat in varchar2 --pat_id/mrn to be validated
)

return varchar2 --returns status + new value (if applicable)

--status: 'C'=CURRENT, 'N'=NEW PT, 'X'=EXCLUDE, ,'O'=OPTED OUT OF RESEARCH, 'F'=NOT FOUND, 'E'=ERROR
--note that 'X'=EXCLUDE could mean "restricted/test/dummy"
--note that 'E'=ERROR will also return the sql error message ilo new value

--------------------------------------------------------------------------------

-- Get new id examples

--------------------------------------------------------------------------------

select f_get_curr_pt(1,'Z000001') from dual;
select f_get_curr_pt(1,pat_id) from i2b2.lz_clarity_patient where rownum = 1;

--------------------------------------------------------------------------------

-- Get new id for a file

--------------------------------------------------------------------------------

select distinct x.*
,i2b2.f_death_clarity_all(coalesce(trim(substr(x.new_pat_id_info,2)),x.clarity_pat_id)) f_death_info
,substr(x.new_pat_id_info,1,1) new_pat_id_stat
,coalesce(x.research_opt_out_pre, p3.research_opt_out) research_opt_out
,coalesce(trim(substr(x.new_pat_id_info,2)),x.clarity_pat_id) new_pat_id
from (select distinct coh.*
,p.pat_id lz_pat_id
,p2.pat_id clarity_pat_id
,case when p2.pat_id is not null then ' ' || p2.pat_id
else f_get_curr_pt(1,coh.pat_id)
end new_pat_id_info
,p.research_opt_out research_opt_out_pre
from xdr_123456_cohpatfin coh
left join i2b2.lz_clarity_patient p on coh.pat_id = p.pat_id
left join clarity.patient p2 on coh.pat_id = p2.pat_id
left join i2b2.bip_project_disclosure bpd on coh.pat_id = bpd.pat_id
and bpd.project_id = 109826
where bpd.pat_id is null
) x
left join i2b2.lz_clarity_patient p3 on coalesce(trim(substr(x.new_pat_id_info,2)),x.clarity_pat_id) = p3.pat_id
;

# Control Cohort Identification

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 11/5/2021)

Code to create controls for a case cohort.

--------------------------------------------------------------------------------

-- Please refer to ctsi_research.p_create_control (sample of Input/output
-- parameters below and note necessary requirements in comments)
--
-- Alternate method to p_create_controls above:
-- C:\\server_apps\\eHonestBroker\\Create_Controls.py
-- pi_project_id = 123456
-- pi_max_number_matches = n, where n=integer of how many controls are requested per case

--------------------------------------------------------------------------------

create or replace PROCEDURE P_CREATE_CONTROL
(pi_project_id IN i2b2.bip_project.project_id%TYPE --Must have a cohort-to-control file that exists in ctsi_research schema, which includes coh_pat_id, ctl_pat_id, and rank
--named xdr_123456_ctlpre, where 123456 is the project_id
--rank must be precoded in the order that PI requests, if any.
--i.e. closest cosine_similarity first
-- see I:\\_BIP\\Consults\\zz Completed\\Hser - Zhu (refresh)\\Hser_Script_Control.sql (Elixhauser)
-- or I:\\_BIP\\Consults\\Bui_CRRT\\Bui_CRRT_Script.sql (Charlson) for examples
,pi_max_number_matches IN INTEGER
) IS
--------------------------------------------------------------------------------
-- Step 1: Match case to control (must contain variables to match (e.g. age, sex)
-- Input: a. Case table
-- b. Preliminary control table
-- Output: xdr_123456_ctlpre
-- Note that rank is only necessary when there is a priority in matching. If
-- there is no priority, this can be set to a sequential number per patient.
--------------------------------------------------------------------------------
drop table xdr_123456_ctlpre purge;

create table xdr_123456_ctlpre as
select distinct cohcase.pat_id coh_pat_id
   ,cohctl.pat_id ctl_pat_id
   ,cohcase.match_age age
   ,cohcase.sex sex
   ,cohcase.race race
   ,cohcase.ethnicity ethnicity
   ,cohctl.number_of_pl_icds
   ,rank() over (partition by cohcase.pat_id order by cohcase.pat_id, cohctl.number_of_pl_icds, cohctl.pat_id) rank
from xdr_123456_coh_patonly_age cohcase -- This is the file that contains the case information
join xdr_123456_ctlprefin cohctl on cohcase.match_age = cohctl.match_age -- This is the file that contains the preliminary control information
and cohcase.sex = cohctl.sex
and cohcase.race = cohctl.race
and cohcase.ethnicity = cohctl.ethnicity
;

--------------------------------------------------------------------------------

-- Step 2: Get final case to control cohort
-- Input: xdr_123456_ctlpre from Step 1
-- Output: xdr_123456_case2ctl
-- NOTE: In the event that PI wants further matching using cosine
-- similarity, please jump to "Rank by cosine similarity"

--------------------------------------------------------------------------------
-- parameters: project_id, #controls to case match
exec p_create_control(123456,2);
select count(*), count(distinct coh_pat_id), count(distinct ctl_pat_id) from xdr_123456_case2ctl; --17829 9085 17829
select * from xdr_123456_case2ctl order by coh_pat_id, ctl_pat_id;

/*****************************************************************************************

******************************************************************************************

******************************************************************************************

Rank by cosine similarity (example only - ignore specific project counts and comments)

E.g. Closest cosine_similarity first - for further examples, see:

I:\\_BIP\\Consults\\zz Completed\\Hser - Zhu (refresh)\\Hser_Script_Control.sql (Elixhauser)

- 1 case to multiple (e.g. 2) controls

I:\\_BIP\\Consults\\Bui_CRRT\\Bui_CRRT_Script.sql (Charlson)

- 1 case to 1 control

******************************************************************************************

******************************************************************************************

*****************************************************************************************/

--------------------------------------------------------------------------------

-- Get current demographics on case cohort

--------------------------------------------------------------------------------

drop table xdr_123456_cohpat2 purge;

create table xdr_123456_cohpat2 as
select distinct coh.pat_id
,p.sex
,p.mapped_race_name race
,p.ethnic_group ethnicity
,case
when x2.death_date_derived = to_date('01/01/0001','mm/dd/yyyy') then null
when x2.death_date_derived <> to_date('01/01/0001','mm/dd/yyyy') then trunc(months_between(x2.death_date_derived, x2.birth_date)/12)
else trunc(months_between(sysdate, x2.birth_date)/12)
end current_or_death_age
,case
when x2.death_date_derived is not null then 'Known Deceased'
else 'Not Known Deceased'
end vital_status
,case
when x2.death_date_derived is not null then x2.death_date_derived
else null
end death_datetime
,p2.pat_name
,p.restricted_yn
,p.pat_mrn_id mrn
,p.birth_date
,p.research_opt_out
from xdr_123456_cohpat coh
join i2b2.lz_clarity_patient p on coh.pat_id = p.pat_id
join clarity.patient p2 on coh.pat_id = p2.pat_id
join (select x1.*
,case
when (dead_pat = 1 or dead_enc = 1 or dead_death = 1) and death_date_min = to_date('12/31/9999','mm/dd/yyyy') then to_date('01/01/0001','mm/dd/yyyy')
when (dead_pat = 1 or dead_enc = 1 or dead_death = 1) and death_date_min <> to_date('12/31/9999','mm/dd/yyyy') then death_date_min
else null
end death_date_derived
from (select distinct x.*
,least(nvl(death_date_pat,to_date('12/31/9999','mm/dd/yyyy'))
,nvl(death_date_enc,to_date('12/31/9999','mm/dd/yyyy'))
,nvl(death_date_death,to_date('12/31/9999','mm/dd/yyyy'))
) death_date_min
from (select coh.pat_id
,coh.pat_mrn_id
,coh.ip_patient_id
,coh.birth_date
,pat.pat_status_c
,ps.name as patient_status
,case when pat.pat_status_c = 2 or pat.death_date is not null then 1 else 0 end dead_pat
,case when enc.pat_id is not null then 1 else 0 end dead_enc
,case when death.pat_id is not null then 1 else 0 end dead_death
,case when nvl(pat.death_date,to_date('01/01/0001','mm/dd/yyyy')) >= pat.birth_date then pat.death_date else null end death_date_pat
,max(enc.hosp_disch_time) over (partition by enc.pat_id) death_date_enc
,case when nvl(death.death_date,to_date('01/01/0001','mm/dd/yyyy')) >= pat.birth_date then death.death_date else null end death_date_death
from xdr_123456_cohpat coh
join clarity.patient pat on coh.pat_id = pat.pat_id
left join clarity.pat_enc_hsp enc on pat.pat_id = enc.pat_id and (enc.disch_disp_c in ('20', '40', '41', '42', '71') or enc.ed_disposition_c = '8')
left join i2b2.lz_death death on pat.pat_id = death.pat_id
left join clarity.zc_patient_status ps on pat.pat_status_c = ps.patient_status_c
) x
) x1
) x2 on coh.pat_id = x2.pat_id
;

alter table xdr_123456_cohpat2 add constraint xdr_123456_cohpat2_pk primary key (pat_id);

select count(*), count(distinct pat_id) from xdr_123456_cohpat2; --2462 2462

select count(*), count(distinct pat_id) from xdr_123456_cohpat; --2464 2464

select * from xdr_123456_cohpat2;

--------------------------------------------------------------------------------

-- Get Charlson comorbidities for case cohort - Final case cohort

--------------------------------------------------------------------------------

drop table xdr_123456_cohpat3 purge;

create table xdr_123456_cohpat3 as
select distinct charl.*
   ,coh.ethnicity
   ,coh.race
   ,coh.sex
   ,coh.vital_status
   ,coh.current_or_death_age
from xdr_123456_cohpat2 coh
join i2b2.lz_clarity_charlson_score charl on coh.pat_id = charl.pat_id
;

select count(*), count(distinct pat_id) from xdr_123456_cohpat3; --2392 2392

--------------------------------------------------------------------------------

-- Get initial controls

--------------------------------------------------------------------------------

drop table xdr_123456_ctlpat1 purge;

create table xdr_123456_ctlpat1 as

select distinct p.pat_id
,p.sex
,p.mapped_race_name race
,p.ethnic_group ethnicity
,case
when x2.death_date_derived = to_date('01/01/0001','mm/dd/yyyy') then null
when x2.death_date_derived <> to_date('01/01/0001','mm/dd/yyyy') then trunc(months_between(x2.death_date_derived, x2.birth_date)/12)
else trunc(months_between(sysdate, x2.birth_date)/12)
end current_or_death_age
,case
when x2.death_date_derived is not null then 'Known Deceased'
else 'Not Known Deceased'
end vital_status
,case
when x2.death_date_derived is not null then x2.death_date_derived
else null
end death_datetime
,p2.pat_name
,p.restricted_yn
,p.pat_mrn_id mrn
,p.birth_date
,p.research_opt_out
from i2b2.lz_clarity_patient p
join clarity.patient p2 on p.pat_id = p2.pat_id
join (select x1.*
,case
when (dead_pat = 1 or dead_enc = 1 or dead_death = 1) and death_date_min = to_date('12/31/9999','mm/dd/yyyy') then to_date('01/01/0001','mm/dd/yyyy')
when (dead_pat = 1 or dead_enc = 1 or dead_death = 1) and death_date_min <> to_date('12/31/9999','mm/dd/yyyy') then death_date_min
else null
end death_date_derived
from (select distinct x.*
,least(nvl(death_date_pat,to_date('12/31/9999','mm/dd/yyyy'))
,nvl(death_date_enc,to_date('12/31/9999','mm/dd/yyyy'))
,nvl(death_date_death,to_date('12/31/9999','mm/dd/yyyy'))
) death_date_min
from (select pat.pat_id
,pat.pat_mrn_id
,pat.birth_date
,pat.pat_status_c
,ps.name as patient_status
,case when pat.pat_status_c = 2 or pat.death_date is not null then 1 else 0 end dead_pat
,case when enc.pat_id is not null then 1 else 0 end dead_enc
,case when death.pat_id is not null then 1 else 0 end dead_death
,case when nvl(pat.death_date,to_date('01/01/0001','mm/dd/yyyy')) >= pat.birth_date then pat.death_date else null end death_date_pat
,max(enc.hosp_disch_time) over (partition by enc.pat_id) death_date_enc
,case when nvl(death.death_date,to_date('01/01/0001','mm/dd/yyyy')) >= pat.birth_date then death.death_date else null end death_date_death
from clarity.patient pat
left join clarity.pat_enc_hsp enc on pat.pat_id = enc.pat_id and (enc.disch_disp_c in ('20', '40', '41', '42', '71') or enc.ed_disposition_c = '8')
left join i2b2.lz_death death on pat.pat_id = death.pat_id
left join clarity.zc_patient_status ps on pat.pat_status_c = ps.patient_status_c
) x
) x1
) x2 on p.pat_id = x2.pat_id
;

alter table xdr_123456_ctlpat1 add constraint xdr_123456_ctlpat1_pk primary key (pat_id);

select count(*), count(distinct pat_id) from xdr_123456_ctlpat1; --6052064 6052064

select * from xdr_123456_ctlpat1;

--------------------------------------------------------------------------------

-- Get Charlson comorbidities for controls

--------------------------------------------------------------------------------

drop table xdr_123456_ctlpat2 purge;

create table xdr_123456_ctlpat2 as
select distinct charl.*
   ,coh.ethnicity
   ,coh.race
   ,coh.sex
   ,coh.vital_status
   ,coh.current_or_death_age
from xdr_123456_ctlpat1 coh
join i2b2.lz_clarity_charlson_score charl on coh.pat_id = charl.pat_id
left join xdr_123456_cohpat cs on coh.pat_id = cs.pat_id --use original 2464-pt cohort to ensure none are used in controls
where cs.pat_id is null
;

select count(*), count(distinct pat_id) from xdr_123456_ctlpat2; --696541 696541

select * from xdr_123456_ctlpat2;

--------------------------------------------------------------------------------

-- Match case to control prior to cosine similarity comparison

--------------------------------------------------------------------------------

drop table xdr_123456_cs2ctlpre purge;

create table xdr_123456_cs2ctlpre as
select distinct cohcase.pat_id coh_pat_id
   ,cohctl.pat_id ctl_pat_id
   ,cohcase.sex
   ,cohcase.race
   ,cohcase.ethnicity
   ,cohcase.current_or_death_age age
   ,cohcase.char_total_score
from xdr_123456_cohpat3 cohcase
join xdr_123456_ctlpat2 cohctl on cohcase.sex = cohctl.sex
and cohcase.race = cohctl.race
and cohcase.ethnicity = cohctl.ethnicity
and cohcase.current_or_death_age = cohctl.current_or_death_age
;

select count(*), count(distinct coh_pat_id), count(distinct ctl_pat_id) from xdr_123456_cs2ctlpre; --2295324 2372 426830

select * from xdr_123456_cs2ctlpre;

--------------------------------------------------------------------------------

-- Rename the columns so I don't have to change each x* reference in the standard cosine_similarity code

--------------------------------------------------------------------------------

alter table xdr_123456_cohpat3 rename column char_myocardial_infarction to x1;

alter table xdr_123456_cohpat3 rename column char_chf to x2;

alter table xdr_123456_cohpat3 rename column char_perivasc to x3;

alter table xdr_123456_cohpat3 rename column char_cerebrovascular_disease to x4;

alter table xdr_123456_cohpat3 rename column char_dementia to x5;

alter table xdr_123456_cohpat3 rename column char_chrnlung to x6;

alter table xdr_123456_cohpat3 rename column char_conn_tiss_rheum_disease to x7;

alter table xdr_123456_cohpat3 rename column char_ulcer to x8;

alter table xdr_123456_cohpat3 rename column char_mild_liver_disease to x9;

alter table xdr_123456_cohpat3 rename column char_dm to x10;

alter table xdr_123456_cohpat3 rename column char_dmcx to x11;

alter table xdr_123456_cohpat3 rename column char_paraplegia_hemiplegia to x12;

alter table xdr_123456_cohpat3 rename column char_renal_disease to x13;

alter table xdr_123456_cohpat3 rename column char_cancer to x14;

alter table xdr_123456_cohpat3 rename column char_mod_or_sev_liver_disease to x15;

alter table xdr_123456_cohpat3 rename column char_metastatic_carcinoma to x16;

alter table xdr_123456_cohpat3 rename column char_aids_hiv to x17;

--

alter table xdr_123456_ctlpat2 rename column char_myocardial_infarction to x1;

alter table xdr_123456_ctlpat2 rename column char_chf to x2;

alter table xdr_123456_ctlpat2 rename column char_perivasc to x3;

alter table xdr_123456_ctlpat2 rename column char_cerebrovascular_disease to x4;

alter table xdr_123456_ctlpat2 rename column char_dementia to x5;

alter table xdr_123456_ctlpat2 rename column char_chrnlung to x6;

alter table xdr_123456_ctlpat2 rename column char_conn_tiss_rheum_disease to x7;

alter table xdr_123456_ctlpat2 rename column char_ulcer to x8;

alter table xdr_123456_ctlpat2 rename column char_mild_liver_disease to x9;

alter table xdr_123456_ctlpat2 rename column char_dm to x10;

alter table xdr_123456_ctlpat2 rename column char_dmcx to x11;

alter table xdr_123456_ctlpat2 rename column char_paraplegia_hemiplegia to x12;

alter table xdr_123456_ctlpat2 rename column char_renal_disease to x13;

alter table xdr_123456_ctlpat2 rename column char_cancer to x14;

alter table xdr_123456_ctlpat2 rename column char_mod_or_sev_liver_disease to x15;

alter table xdr_123456_ctlpat2 rename column char_metastatic_carcinoma to x16;

alter table xdr_123456_ctlpat2 rename column char_aids_hiv to x17;

--------------------------------------------------------------------------------

-- Get cosine_similarity (measure of similarity between case and control)

-- cosine_similarity = 0: no match whatsoever

-- cosine_similarity < 1: possible match; the higher, the more similar

-- cosine_similarity = 1: exact match

-- cosine_similarity > 1: should never happen but check to confirm

--------------------------------------------------------------------------------

drop table xdr_123456_ctlpat_cs2 purge;

create table xdr_123456_ctlpat_cs2 as
select x.*
,cs_num / (sqrt(cs_den_coh) * sqrt(cs_den_ctl)) as cosine_similarity
from (select cc.*
,(coh.x1*ctl.x1)+(coh.x2*ctl.x2)+(coh.x3*ctl.x3)+(coh.x4*ctl.x4)+(coh.x5*ctl.x5)
+(coh.x6*ctl.x6)+(coh.x7*ctl.x7)+(coh.x8*ctl.x8)+(coh.x9*ctl.x9)+(coh.x10*ctl.x10)
+(coh.x11*ctl.x11)+(coh.x12*ctl.x12)+(coh.x13*ctl.x13)+(coh.x14*ctl.x14)+(coh.x15*ctl.x15)
+(coh.x16*ctl.x16)+(coh.x17*ctl.x17)
as cs_num
,(power(coh.x1,2))+(power(coh.x2,2))+(power(coh.x3,2))+(power(coh.x4,2))+(power(coh.x5,2))
+(power(coh.x6,2))+(power(coh.x7,2))+(power(coh.x8,2))+(power(coh.x9,2))+(power(coh.x10,2))
+(power(coh.x11,2))+(power(coh.x12,2))+(power(coh.x13,2))+(power(coh.x14,2))+(power(coh.x15,2))
+(power(coh.x16,2))+(power(coh.x17,2))
as cs_den_coh
,(power(ctl.x1,2))+(power(ctl.x2,2))+(power(ctl.x3,2))+(power(ctl.x4,2))+(power(ctl.x5,2))
+(power(ctl.x6,2))+(power(ctl.x7,2))+(power(ctl.x8,2))+(power(ctl.x9,2))+(power(ctl.x10,2))
+(power(ctl.x11,2))+(power(ctl.x12,2))+(power(ctl.x13,2))+(power(ctl.x14,2))+(power(ctl.x15,2))
+(power(ctl.x16,2))+(power(ctl.x17,2))
as cs_den_ctl
from xdr_123456_cs2ctlpre cc
join xdr_123456_cohpat3 coh on cc.coh_pat_id = coh.pat_id --case pts
join xdr_123456_ctlpat2 ctl on cc.ctl_pat_id = ctl.pat_id --ctl pts
) x
where cs_den_coh > 0 and cs_den_ctl > 0
;

alter table xdr_123456_ctlpat_cs2 add constraint xdr_123456_ctlpat_cs2_pk primary key (coh_pat_id, ctl_pat_id);

select count(*), count(distinct coh_pat_id), count(distinct ctl_pat_id) from xdr_123456_ctlpat_cs2; --2295324 2372 426830

select * from xdr_123456_ctlpat_cs2;

--------------------------------------------------------------------------------

-- Verify cosine_similarity

--------------------------------------------------------------------------------

select * from xdr_123456_ctlpat_cs2;

select cs, count(*)
from (select case
when cosine_similarity = 0 then '=0' --no match whatsoever
when cosine_similarity < 1 then '<1' --possible match; the higher, the more similar
when cosine_similarity = 1 then '=1' --exact match
when cosine_similarity > 1 then '>1' --should never happen but checking to confirm
end cs
from xdr_123456_ctlpat_cs2
)
group by cs
;

--=0 1042910
--=1 3330
--<1 1249084

select * from xdr_123456_ctlpat_cs2 where cosine_similarity = 0;

select * from xdr_123456_ctlpat_cs2 where cosine_similarity = 1;

select * from xdr_123456_ctlpat_cs2 where cosine_similarity > 0 and cosine_similarity < 1;

--------------------------------------------------------------------------------

-- Prepare final controls

-- At this time, determine how many controls you need per case.

-- If 1 case to 1 control, jump to "Prepare final controls for 1 case to 1 control"

-- If 1 case to multiple controls, jump to "Prepare final controls for 1 case to many controls"

--------------------------------------------------------------------------------

/*****************************************************************************************

Prepare final controls for 1 case to 1 control

*****************************************************************************************/

--------------------------------------------------------------------------------

-- Prepare input to creating control procedure
-- Start with ranking within each case cohort patient

--------------------------------------------------------------------------------

drop table xdr_123456_ctlpre1 purge; --must include coh_pat_id, ctl_pat_id, and rank

create table xdr_123456_ctlpre1 as
select x.*
,rank() over (partition by coh_pat_id order by coh_pat_id, rank_ctl, rank_coh) rank
from (select distinct coh_pat_id
,rank() over (partition by coh_pat_id order by coh_pat_id, cosine_similarity desc, char_total_score desc, ctl_pat_id) rank_coh
,rank() over (partition by ctl_pat_id order by ctl_pat_id, cosine_similarity desc, char_total_score desc, coh_pat_id) rank_ctl
,cosine_similarity
,char_total_score
,ctl_pat_id
from xdr_123456_ctlpat_cs2
where cosine_similarity > 0
) x

;

alter table xdr_123456_ctlpre1 add constraint xdr_123456_ctlpre1_pk primary key (coh_pat_id, rank, ctl_pat_id);

select count(*), count(distinct coh_pat_id), count(distinct ctl_pat_id) from xdr_123456_ctlpre1; --1252414 2364 334474

select * from xdr_123456_ctlpre1;

select count(*) --4328=good=all coh and ctl pts are unique
from (select coh_pat_id from xdr_123456_ctlpre1 where rank_coh = 1 and rank_ctl = 1 and rank = 1
union select ctl_pat_id from xdr_123456_ctlpre1 where rank_coh = 1 and rank_ctl = 1 and rank = 1
)
;

--------------------------------------------------------------------------------

-- Now save the prioritized ones

--------------------------------------------------------------------------------

drop table xdr_123456_ctlpre2 purge; --must include coh_pat_id, ctl_pat_id, and rank

create table xdr_123456_ctlpre2 as

select x.*
from xdr_123456_ctlpre1 x
where rank_coh = 1 and rank_ctl = 1 and rank = 1
order by ctl_pat_id, cosine_similarity desc
;

alter table xdr_123456_ctlpre2 add constraint xdr_123456_ctlpre2_pk primary key (coh_pat_id, rank, ctl_pat_id);

select count(*), count(distinct coh_pat_id), count(distinct ctl_pat_id) from xdr_123456_ctlpre2; --2164 2164 2164

select * from xdr_123456_ctlpre2;

--------------------------------------------------------------------------------

-- Now get the leftovers for the control matching procedure
-- The prioritized ones are excluded because they are already the top
-- matches per case

--------------------------------------------------------------------------------

drop table xdr_123456_ctlpre purge;

create table xdr_123456_ctlpre as
select x.*
from xdr_123456_ctlpre1 x
left join xdr_123456_ctlpre2 sav on x.coh_pat_id = sav.coh_pat_id
or x.ctl_pat_id = sav.ctl_pat_id
where sav.coh_pat_id is null
;

alter table xdr_123456_ctlpre add constraint xdr_123456_ctlpre_pk primary key (coh_pat_id, rank, ctl_pat_id);

select count(*), count(distinct coh_pat_id), count(distinct ctl_pat_id) from xdr_123456_ctlpre; --96256 200 68044

select * from xdr_123456_ctlpre;

--------------------------------------------------------------------------------

-- Jump to "Run procedure to get unique controls per case"

--------------------------------------------------------------------------------

/*****************************************************************************************

Prepare final controls for 1 case to many controls

*****************************************************************************************/

--------------------------------------------------------------------------------

-- Prepare input to creating control procedure

-- Start with ranking within each case cohort patient

--------------------------------------------------------------------------------

drop table xdr_123456_ctlpre1 purge; --must include coh_pat_id, ctl_pat_id, and rank

create table xdr_123456_ctlpre1 as

select distinct coh_pat_id
,rank() over (partition by coh_pat_id order by coh_pat_id, cosine_similarity desc, ctl_pat_id) rank
,cosine_similarity
,ctl_pat_id
from xdr_123456_ctlpat_cs2
;

alter table xdr_123456_ctlpre1 add constraint xdr_123456_ctlpre1_pk primary key (coh_pat_id, rank, ctl_pat_id);

select count(*), count(distinct coh_pat_id), count(distinct ctl_pat_id) from xdr_123456_ctlpre1; --164575792 9559 622321

select * from xdr_123456_ctlpre1;

--------------------------------------------------------------------------------

-- Get top n controls per case to alleviate the data inputted into the procedure

--------------------------------------------------------------------------------

drop table xdr_123456_ctlpre purge; --must include coh_pat_id, ctl_pat_id, and rank

create table xdr_123456_ctlpre as
select ctl.*
from xdr_123456_ctlpre1 ctl
where ctl.rank <= 5000 --change this at your discretion, increase and rerun if not enough controls reached

;

alter table xdr_123456_ctlpre add constraint xdr_123456_ctlpre_pk primary key (coh_pat_id, rank, ctl_pat_id);

select count(*), count(distinct coh_pat_id), count(distinct ctl_pat_id) from xdr_123456_ctlpre; --45352259 9559 613073

select * from xdr_123456_ctlpre;

--------------------------------------------------------------------------------

-- Jump to "Run procedure to get unique controls per case"

--------------------------------------------------------------------------------

/*****************************************************************************************

-- Run procedure to get unique controls per case
-- (pi_project_id in i2b2.bip_project.project_id%type --Must have a cohort-to-control file that exists in ctsi_research schema, which includes coh_pat_id, ctl_pat_id, and rank
-- --named xdr_pppppp_ctlpre, where pppppp is the project_id
-- --rank must be precoded in the order that PI requests, if any.
-- -- see I:\\_BIP\\Consults\\zz Completed\\Hser - Zhu (refresh)\\Hser_Script_Control.sql (Elixhauser)
-- -- or I:\\_BIP\\Consults\\Bui_CRRT\\Bui_CRRT_Script.sql (Charlson) for examples
-- ,pi_max_number_matches in integer
-- )

-- Alternate method to p_create_controls above:

-- C:\\server_apps\\eHonestBroker\\Create_Controls.py

-- pi_project_id = 123456

-- pi_max_number_matches = n, where n=integer of how many controls are requested per case

*****************************************************************************************/

exec p_create_control(123456,n); --where n=integer of how many controls are requested per case

--------------------------------------------------------------------------------

-- Verify procedure results

--------------------------------------------------------------------------------

select count(*), count(distinct coh_pat_id), count(distinct ctl_pat_id) from xdr_123456_case2ctl; --200 200 200

select * from xdr_123456_case2ctl;

select rank, count(*) from xdr_123456_case2ctl group by rank order by rank;

--1 178
--2 20
--3 2

select min(cosine_similarity) from xdr_123456_case2ctl; --0.0.3333333333333333333333333333333333333344

select min(cosine_similarity) from xdr_123456_case2ctl where rank = 1; --0.0.4472135954999579392818347337462552470882

select min(cosine_similarity) from xdr_123456_case2ctl where rank = 2; --0.0.3333333333333333333333333333333333333344

select min(cosine_similarity) from xdr_123456_case2ctl where rank = 3; --0.0.816496580927726032732428024901963797324

select * --no rows found=good=all unique
from xdr_123456_case2ctl coh
left join xdr_123456_case2ctl ctl on coh.coh_pat_id = ctl.ctl_pat_id
left join xdr_123456_case2ctl cs on coh.ctl_pat_id = cs.coh_pat_id
where ctl.ctl_pat_id is not null
or cs.coh_pat_id is not null
;

--------------------------------------------------------------------------------

-- Get final controls

--------------------------------------------------------------------------------

drop table xdr_123456_cohctlpat purge;

create table xdr_123456_cohctlpat as

select distinct z.*
,p.pat_mrn_id coh_mrn
,p2.pat_mrn_id ctl_mrn
from
-- (select x.* from xdr_123456_ctlpre2 x --uncomment if case:control ratio is 1:1, you need this to reinstate the exact matches you saved earlier
-- union --uncomment if case:control ratio is 1:1, you need this to reinstate the exact matches you saved earlier
-- select x.* from --uncomment if case:control ratio is 1:1, you need this to reinstate the exact matches you saved earlier
xdr_123456_case2ctl
-- x --uncomment if case:control ratio is 1:1, you need this to reinstate the exact matches you saved earlier
-- ) --uncomment if case:control ratio is 1:1, you need this to reinstate the exact matches you saved earlier
z
join i2b2.lz_clarity_patient p on z.coh_pat_id = p.pat_id
join i2b2.lz_clarity_patient p2 on z.ctl_pat_id = p2.pat_id
;

select count(*), count(distinct coh_pat_id), count(distinct ctl_pat_id) from xdr_123456_cohctlpat; --2364 2364 2364

select * from xdr_123456_cohctlpat;

-- Verify unique cases and controls

select count(*) --4728=good=all coh and ctl pts are unique
from (select coh_pat_id from xdr_123456_cohctlpat
union select ctl_pat_id from xdr_123456_cohctlpat
)
;

# Vaccination

(by [Ngan Chau](https://uclabip.atlassian.net/wiki/people/557058:6a532fda-2b06-4abd-8615-2f07f4bd7f9f?ref=confluence) on 1/24/2022)

– the following code retrieves the patients' vaccination records (test)
– this data can be added to the vaccination data retrieved through the ‘procedures’

drop table xdr_PRINV_immunizations purge;

create table xdr_PRINV_immunizations as
select i1.*
,i2.name    immunization
,istat.name immunization_status
,penc.pat_enc_csn_id
,penc.visit_prov_id
,penc.department_id
,cpt.cpt_code
from xdr_PRINV_pat     pat
join immune            i1 on pat.pat_id=i1.pat_id
join clarity_immunzatn i2 on i1.immunzatn_id=i2.immunzatn_id
join zc_immnztn_status istat on istat.immnztn_status_c = i1.immnztn_status_c
join pat_enc penc on i1.imm_csn = penc.pat_enc_csn_id
left join i2b2.lz_clarity_cpt_code cpt on cpt.pat_enc_csn_id=penc.pat_enc_csn_id
where trunc(immune_date) <insert date param logic>
and regexp_like(i2.name,'+(hpv|tdap|meningococcal|flu|Gardasil|mcv4)+','i') – name of vaccines
and istat.name = 'Given'
and regexp_like(penc.department_id,'12345|67890');

commit;      

# ICU Care

(by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 3/4/2022)

– the following code flags ICU care based on encoutner name, level of care, or accommodation.

-------------------------------------------

-- Identify Intensive Care Unit stays/care

-------------------------------------------

SELECT adt.*
from clarity_adt adt
left join ZC_LVL_OF_CARE zloc on adt.PAT_LVL_OF_CARE_C = zloc.LEVEL_OF_CARE_C
left join ZC_ACCOMMODATION za on adt.ACCOMMODATION_C = za.ACCOMMODATION_C
left join clarity_dep dept on adt.department_id = dept.department_id
where department_name like '%ICU%'
OR ( adt.PAT_LVL_OF_CARE_C IS NOT NULL AND adt.PAT_LVL_OF_CARE_C = '6') -- ICU
OR adt.ACCOMMODATION_C IN (25 --ICU
28 --ICU Trauma
46 --ICU Pediatric
);

# SmartData Elements

(by ​[Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 7/30/2022)

This link includes the list of [SmartData Elements in EPIC](https://galaxy.epic.com/Search/GetFile?url=1!68!100!1005313&rank=5&queryid=96416879&docid=61648) and the actual file is under I:\\Bell_BIP\\xDR\\Lists and Codes\\New Standard SmartData Elements.xlsx.

The caveat is that this list is the generic one from EPIC which means that customized smartdata elements created at UCLA would not be included here.

The following code is just a very first draft to pull elements but further enhancements might be needed to optimize the data pull.

-------------------------------------------

-- Extract SmartData Elements information

-------------------------------------------

select distinct coh.pat_id
,sed.ELEMENT_ID
-- once we have the table referenced above loaded into our schema, we can simply left join to it and get the name of the element
-- based on the element ID, the example below was hardcoded becase our team was involved in the creation of the form
-- and were told the element IDs behind each question
-- ,case
-- when sed.ELEMENT_ID = 'EPIC#31000090752' then 'AD < 3 YEARS OLD IN CHART'
-- when sed.ELEMENT_ID = 'UCLATEAM#330' then 'POLST < 3 YEARS OLD IN CHART'
-- when sed.ELEMENT_ID = 'UCLATEAM#331' then 'AD < 3 YEARS OLD AT HOME COMPLETED'
-- when sed.ELEMENT_ID = 'UCLATEAM#332' then 'AD > 3 YEARS OLD ON FILE STILL VALID'
-- when sed.ELEMENT_ID = 'UCLATEAM#333' then 'WILLING TO USE PREPARE FOR YOUR CARE WEBSITE'
-- when sed.ELEMENT_ID = 'UCLATEAM#336' then 'INTRODUCING AD COMPLETION WITH PATIENT'
-- when sed.ELEMENT_ID = 'UCLATEAM#338' then 'IS PATIENT WILLING TO USE PREPARE FOR YOUR CARE WEBSITE'
-- when sed.ELEMENT_ID = 'UCLATEAM#339' then 'ACP OUTREACH TIMESTAMP'
-- when sed.ELEMENT_ID = 'UCLATEAM#341' then 'PATIENT WILL COMPLETE AD AND SUBMIT VIA:'
-- when sed.ELEMENT_ID = 'UCLATEAM#342' then 'PATIENT WILL COMPLETE AD AND SUBMIT VIA OTHER COMMENT:'
-- when sed.ELEMENT_ID = 'UCLATEAM#344' then 'AD < 3 YEARS OLD AT HOME COMPLETED OTHER COMMENT'
-- when sed.ELEMENT_ID = 'UCLATEAM#345' then 'PATIENT NOT READY TO PROCEED WITH AD. CCC TO FOLLOW UP ON:'
-- when sed.ELEMENT_ID = 'UCLATEAM#346' then 'POLST'
-- when sed.ELEMENT_ID = 'UCLATEAM#347' then 'PATIENT REFUSE'
-- when sed.ELEMENT_ID = 'UCLATEAM#348' then 'OUTREACH COMMENTS'
-- else 'OTHER'
-- end smart_element_name
,sed.CUR_SOURCE_LQF_ID
,sed.CUR_VALUE_DATETIME
,sed.UPDATE_DATE
,sed.REC_ARCHIVED_YN
,sed.CUR_VAL_UTC_DTTM
,sed.HLV_ID
,sev.line
,sev.smrtdta_elem_value
,CUR_SOURCE_LQF_ID
-- ,sysdate as creation_date
-- if you need to pull te user who filled out the form
-- ,sed.CUR_VALUE_USER_ID as user_id
-- ,emp.name as user_name
from i2b2.lz_clarity_patient coh
join clarity.smrtdta_elem_data sed on coh.pat_id = sed.PAT_LINK_ID
join clarity.SMRTDTA_ELEM_VALUE sev on sed.hlv_id = sev.hlv_id
--left join clarity.clarity_emp emp on sed.CUR_VALUE_USER_ID = emp.USER_ID
where
-- if we know the form ID
--sed.CUR_SOURCE_LQF_ID = 992' --ACP outreach
sed.ELEMENT_ID = 'EPIC#31000090752'

# Birth Control / Contraception / Sexually Active

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 8/10/2022)

– the following gets birth control/contraception and sexually active data for patients

select distinct coh.pat_id
   ,xsa.name sexually_active
   ,s.contact_date
   ,max(s.contact_date) over (partition by coh.pat_id) latest_contact_date
   ,s.condom_yn
   ,s.pill_yn
   ,s.diaphragm_yn
   ,s.iud_yn
   ,s.surgical_yn
   ,s.spermicide_yn
   ,s.implant_yn
   ,s.rhythm_yn
   ,s.injection_yn
   ,s.sponge_yn
   ,s.inserts_yn
   ,s.abstinence_yn
from xdr_123456_cohpat coh
join social_hx s on coh.pat_id = s.pat_id
left join zc_sexually_active xsa on s.sexually_active_c = xsa.sexually_active_c
-- where xsa.name = 'Yes'
;

# PHI disclosure HHS Guidelines

(by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 10/10/2022)

IRB can supersede these guidelines since every case is evaluated separately but when an IRB and/or Compliance Approval do not apply (ex counts) please use this information.

[https://www.hhs.gov/hipaa/for-professionals/privacy/special-topics/de-identification/index.html#zip](https://www.hhs.gov/hipaa/for-professionals/privacy/special-topics/de-identification/index.html#zip)

# Provider Email

(by [Cenan Pirani](https://uclabip.atlassian.net/wiki/people/5f5922bcff2fe2006fb700d8?ref=confluence) on 10/20/2022)

### Get provider emails using the ‘prov_id’:

select distinct emp.prov_id
,lower(substr(vu.ad_username,4)) || '@mednet.ucla.edu' as prov_email
from cohort_enc enc
join clarity_emp emp on enc.visit_prov_id = emp.prov_id
join v_cube_d_user vu on emp.user_id = vu.user_id
;

# ICD-10-CM Updates to Lookup Table

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 10/24/2022)

### Update ICD-10 dx lookup table:

Steps:

1.  Download ICD-10 file from [cms.gov](http://cms.gov) and search for icd-10.
2.  Run python program below to create a new file (ICD-10 CM codes in this file typically do not contain the period). Note that this is required for ICD-10 CM diagnosis codes, and not ICD-10 PCS procedure codes. ICD-10 PCS procedure codes can simply be downloaded from [cms.gov](http://cms.gov) and imported into the icd10* table as one column (COLUMN1) to be loaded into the I2B2.LZ_DX_PX_LOOKUP table.
3.  Run sql script below to insert codes that do not exist in I2B2.LZ_DX_PX_LOOKUP table.

"""

@author: ttacorda

"""

import os

filepath = ("I:\\\\_Theona\\\\icd10_dx_updates\\\\2023_Updates\\\\2023 Code Descriptions in Tabular Order\\\\")
filein = str(filepath + 'icd10cm_codes_2023.txt')
fileout = str(filepath + 'icd10cm_codes_2023.txt_new.csv')

# Delete the formatted file first, if one exists

if os.path.exists(fileout):
os.remove(fileout)
print('starting...')
writeFile = open(fileout, 'w', encoding='utf8')
i=0
writeFile.write('"icd_code","icd_description"\\n')
with open(filein, errors='ignore') as readFile:

for line in readFile:

try:
ln = str(line)
if len(ln\[0:8\].strip(" ")) == 3:
icd = ln\[0:3\]
else:
icd = ln\[0:3\] + '.' + ln\[3:8\]
desc = ln\[8:-1\]

writeFile.write('"' + icd + '","' + desc + '"\\n')

i += 1

except:

print('An error occurred at line ' + i)
# After this program is run, import to i2b2.icd10_yyyy, where yyyy is the icd-10 import year.
readFile.close()
writeFile.close()
print('ending...')

-----------------------------------------------------------------------------------------------------

-- Housekeeping

-----------------------------------------------------------------------------------------------------

-- Import from cms.gov (CM or PCS ICD-10 codes)

drop table icd10_2023 purge;

select count(*) from i2b2.icd10_2023; --73639

select * from i2b2.icd10_2023;

select * from i2b2.lz_dx_px_lookup;

-----------------------------------------------------------------------------------------------------

-- ICD-10 CM diagnosis code refresh

-----------------------------------------------------------------------------------------------------

insert into i2b2.lz_dx_px_lookup (code, code_type, icd_type, icd_desc)

select trim(icd_code) code
   ,'DX' code_type
   ,10 icd_type
   ,trim(icd_description) icd_desc
from i2b2.icd10_2023 x
left join i2b2.lz_dx_px_lookup dl on trim(x.icd_code) = dl.code
and dl.code_type = 'DX'
and dl.icd_type = 10
where dl.code is null
;

commit; --1,343 rows inserted.

-----------------------------------------------------------------------------------------------------

-- ICD-10 PCS procedure code refresh

-----------------------------------------------------------------------------------------------------

insert into i2b2.lz_dx_px_lookup (code, code_type, icd_type, icd_desc)
select substr(column1,1,7) code
,'PX' code_type
,10 icd_type
,trim(substr(column1,8)) icd_desc
from i2b2.icd10_2023 x
left join i2b2.lz_dx_px_lookup dl on substr(x.column1,1,7) = dl.code
and dl.code_type = 'PX'
and dl.icd_type = 10
where dl.code is null
;

commit; --7,772 rows inserted.

-----------------------------------------------------------------------------------------------------

-- Verification and cleanup

-----------------------------------------------------------------------------------------------------

drop table icd10_2023 purge;

select code_type, count(*) from i2b2.lz_dx_px_lookup where icd_type = 10 group by code_type order by code_type;

select * from i2b2.lz_dx_px_lookup where icd_desc is null; --no rows found=good

# Escape special characters in large texts

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 12/12/2022)

Steps:

1.  Create text tables (e.g. notes, pathology results, imaging narratives/impressions) named xdr_123456_entity_cd, where 123456 is the project_id and entity_cd is the element (e.g. NOTE, PATHR, IMGN/IMGI, respectively).
2.  Run python program below to create a new text files. Change project id and entity codes accordingly. This program is also located in I:_BIP\\xDR\\Python\\escape_text_from_oracle_to_redcapcsv.py
3.  Provide new files to DOMSTATS or load directly to REDCap, whichever is appropriate for your project.

# -*- coding: utf-8 -*-

"""

Created on Wed Apr 7 05:58:27 2021

@author: TheonaT1

"""

# import commands

import os
import cx_Oracle as Ora

# import csv

filepath = ("E:\\\\Projects\\\\123456\\\\Data\\\\")

print('starting...')

batch_size = 100000

# Format date

CLdb = Ora.connect('ctsi_research/S5archR3_CTsi@clarityq')
cur = CLdb.cursor()
sql = 'alter session set nls_date_format = \\'MM/DD/YYYY HH24:MI\\'';
cur.execute(sql)

# -----------------------------------------------------------------------------

# Provider_Notes

# -----------------------------------------------------------------------------

print('starting Provider_Notes...')
fileout = str(filepath + 'Provider_Notes_REDCap_Final.txt')

# Delete output file if it exists

if os.path.exists(fileout):
os.remove(fileout)
writeFile = open(fileout, 'w', encoding='utf8', errors="ignore")

# Write header

v_col = 'ip_patient_id,ip_enc_id,ip_note_id,note_type,create_datetime,create_by,line_number,note_text,contact_date'
writeFile.write(v_col + '\\n')

# Get and Write rows

sql = 'select \\'"\\' || study_id ' \\
'|| \\'"\\' || \\'|\\' || \\'"\\' || study_csn ' \\
'|| \\'"\\' || \\'|\\' || \\'"\\' || study_note_id ' \\
'|| \\'"\\' || \\'|\\' || \\'"\\' || note_type ' \\
'|| \\'"\\' || \\'|\\' || \\'"\\' || create_datetime ' \\
'|| \\'"\\' || \\'|\\' || \\'"\\' || create_by ' \\
'|| \\'"\\' || \\'|\\' || \\'"\\' || line_number ' \\
'|| \\'"\\' || \\'|\\' || \\'"\\' || note_text ' \\
'|| \\'"\\' || \\'|\\' || \\'"\\' || contact_date ' \\
'|| \\'"\\' export ' \\
'from (select distinct study_id, study_csn, study_note_id, note_type, create_datetime' \\
', create_by, line_number, note_text, contact_date ' \\
'from xdr_123456_note ' \\
'order by study_id, study_note_id, line_number)';

# print(sql)

i=0

try:

with CLdb.cursor() as cur:
cur.execute(sql)
while True:

rows = cur.fetchmany(batch_size)
if not rows:
break

for row in rows:
v_col = row\[0\]
v_col = v_col.replace('"', '', 1) # reset first "
v_col = v_col\[:-1\] # remove last "
v_col = v_col.replace('"|"', '|||xxxyyz') # remove double-quotes and change delimiter
v_col = v_col.replace('\\\\', '\\\\\\\\') # escape backslashes
v_col = v_col.replace('\\"', '""') # escape double-quotes
v_col = v_col.replace('|||xxxyyz', '","') # reset all delimiters back to commas
v_col = '"' + v_col # reinstate first "
v_col = v_col + '"' # reinstate last "
writeFile.write(v_col + '\\n')

i += 1

except CLdb.Error as error:

print(error)
writeFile.close()
print('ending Provider_Notes...')

# -----------------------------------------------------------------------------
# Pathology_Results
# -----------------------------------------------------------------------------

print('starting Pathology_Results...')

fileout = str(filepath + 'Pathology_Results_REDCap_Final3.txt')

# Delete output file if it exists

if os.path.exists(fileout):
os.remove(fileout)
writeFile = open(fileout, 'w', errors="ignore")
# Write header
v_col = 'ip_patient_id,ip_order_proc_id,component_id,component_name,sort_key,result_txt'
writeFile.write(v_col + '\\n')

# Get and Write rows

sql = 'select \\'"\\' || study_id ' \\
'|| \\'"\\' || \\'|\\' || \\'"\\' || study_order_proc_id ' \\
'|| \\'"\\' || \\'|\\' || \\'"\\' || component_id ' \\
'|| \\'"\\' || \\'|\\' || \\'"\\' || component_name ' \\
'|| \\'"\\' || \\'|\\' || \\'"\\' || sort_key ' \\
'|| \\'"\\' || \\'|\\' || \\'"\\' || result_txt ' \\
'|| \\'"\\' export ' \\
'from (select distinct study_id, study_order_proc_id, component_id, component_name' \\
', sort_key, result_txt ' \\
'from xdr_123456_pathr ' \\
'order by study_id, study_order_proc_id, component_id, sort_key)';

# print(sql)

i=0

try:
with CLdb.cursor() as cur:
cur.execute(sql)
while True:
rows = cur.fetchmany(batch_size)
if not rows:
break
for row in rows:
v_col = row\[0\]
v_col = v_col.replace('"', '', 1) # reset first "
v_col = v_col\[:-1\] # remove last "
v_col = v_col.replace('"|"', '|||xxxyyz') # remove double-quotes and change delimiter
v_col = v_col.replace('\\\\', '\\\\\\\\') # escape backslashes
v_col = v_col.replace('\\"', '""') # escape double-quotes
v_col = v_col.replace('|||xxxyyz', '","') # reset all delimiters back to commas
v_col = '"' + v_col # reinstate first "
v_col = v_col + '"' # reinstate last "
writeFile.write(v_col + '\\n')

i += 1
except CLdb.Error as error:
print(error)
writeFile.close()
print('ending Pathology_Results...')

# -----------------------------------------------------------------------------
# Imaging_Impressions
# -----------------------------------------------------------------------------

print('starting Imaging_Impressions...')

fileout = str(filepath + 'Imaging_Impressions_REDCap_Final.txt')

# Delete output file if it exists

if os.path.exists(fileout):
os.remove(fileout)
writeFile = open(fileout, 'w', errors="ignore")
# Write header

v_col = 'ip_patient_id,ip_order_proc_id,line,impression'

writeFile.write(v_col + '\\n')

# Get and Write rows

sql = 'select \\'"\\' || study_id ' \\
'|| \\'"\\' || \\'|\\' || \\'"\\' || study_order_proc_id ' \\
'|| \\'"\\' || \\'|\\' || \\'"\\' || line ' \\
'|| \\'"\\' || \\'|\\' || \\'"\\' || impression ' \\
'|| \\'"\\' export ' \\
'from (select distinct study_id, study_order_proc_id, line, impression ' \\
'from xdr_123456_imgi ' \\
'order by study_id, study_order_proc_id, line)';

# print(sql)

i=0
try:
with CLdb.cursor() as cur:
cur.execute(sql)
while True:
rows = cur.fetchmany(batch_size)
if not rows:
break
for row in rows:
v_col = row\[0\]
v_col = v_col.replace('"', '', 1) # reset first "
v_col = v_col\[:-1\] # remove last "
v_col = v_col.replace('"|"', '|||xxxyyz') # remove double-quotes and change delimiter
v_col = v_col.replace('\\\\', '\\\\\\\\') # escape backslashes
v_col = v_col.replace('\\"', '""') # escape double-quotes
v_col = v_col.replace('|||xxxyyz', '","') # reset all delimiters back to commas
v_col = '"' + v_col # reinstate first "
v_col = v_col + '"' # reinstate last "
writeFile.write(v_col + '\\n')

i += 1
except CLdb.Error as error:
print(error)
writeFile.close()
print('ending Imaging_Impressions...')

# -----------------------------------------------------------------------------
# Imaging_Narratives
# -----------------------------------------------------------------------------

print('starting Imaging Narratives...')
fileout = str(filepath + 'Imaging_Narratives_REDCap_Final.txt')
# Delete output file if it exists
if os.path.exists(fileout):
os.remove(fileout)
writeFile = open(fileout, 'w', errors="ignore")
# Write header
v_col = 'ip_patient_id,ip_order_proc_id,line,narrative'
writeFile.write(v_col + '\\n')
# Get and Write rows
sql = 'select \\'"\\' || study_id ' \\
'|| \\'"\\' || \\'|\\' || \\'"\\' || study_order_proc_id ' \\
'|| \\'"\\' || \\'|\\' || \\'"\\' || line ' \\
'|| \\'"\\' || \\'|\\' || \\'"\\' || narrative ' \\
'|| \\'"\\' export ' \\
'from (select distinct study_id, study_order_proc_id, line, narrative ' \\
'from xdr_123456_imgn ' \\
'order by study_id, study_order_proc_id, line)';
# print(sql)
i=0
try:
with CLdb.cursor() as cur:
cur.execute(sql)
while True:
rows = cur.fetchmany(batch_size)
if not rows:
break
for row in rows:
v_col = row\[0\]
v_col = v_col.replace('"', '', 1) # reset first "
v_col = v_col\[:-1\] # remove last "
v_col = v_col.replace('"|"', '|||xxxyyz') # remove double-quotes and change delimiter
v_col = v_col.replace('\\\\', '\\\\\\\\') # escape backslashes
v_col = v_col.replace('\\"', '""') # escape double-quotes
v_col = v_col.replace('|||xxxyyz', '","') # reset all delimiters back to commas
v_col = '"' + v_col # reinstate first "
v_col = v_col + '"' # reinstate last "
writeFile.write(v_col + '\\n')
i += 1
except CLdb.Error as error:
print(error)
writeFile.close()
print('ending Imaging_Narratives...')
print('ending...')

# Splitting table data into smaller files

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 12/14/2022)

Steps:

1.  Create table named xdr_123456_entity_cd, where 123456 is the project_id and entity_cd is the element (e.g. NOTE, PATHR, IMGN/IMGI, respectively). Need to create RN column for rownum (example below).
2.  Run python program below to create a multiple files. Change project id and entity codes accordingly. This program is also located in I:_BIP\\xDR\\Python\\split_oracle_tables_to_mult_files_4redcap.py. Note that this program also takes care of escaping special characters in large text files.

drop table xdr_123456_imgn_rc purge;

create table xdr_123456_imgn_rc as
select rownum rn
,x.*
from (select distinct coh.study_id_rc "study_id"
,'imaging_narratives' "redcap_repeat_instrument"
,row_number() over (partition by x.study_id order by x.study_id, x.study_order_proc_id, x.line) as "redcap_repeat_instance"
,x.study_order_proc_id "ip_order_proc_id_narrative"
,x.line "narrative_line"
,x.narrative "narrative"
,0 "imaging_narratives_complete"
from xdr_123456_DEMO_rc coh
join xdr_123456_IMGN x on coh.study_id = x.study_id
order by coh.study_id_rc, x.study_order_proc_id, "redcap_repeat_instance"
) x
;

import os
import cx_Oracle as Ora
print('starting...')

#------------------------------------------------------------------------------

# Start customizations!!!
# Pre-requisite: Create xdr_123456_entity_cd_rc,
# where 123456=project_id
# and entity_cd = entity_cd (e.g. IMGN=imaging narratives)

#------------------------------------------------------------------------------

filepath = ("I:\\\\_BIP\\\\Consults\\\\")
batch_size = 100000
v_limit = 14000
v_projid = '123456'
v_entcd = 'IMGN'
v_file = 'Imaging_Narratives_'
v_hdr = 'study_id,redcap_repeat_instrument,redcap_repeat_instance,ip_order_proc_id_narrative,narrative_line,narrative,imaging_narratives_complete'
sql = 'select rn, \\'"\\' || "study_id" ' \\
'|| \\'"\\' || \\'|\\' || \\'"\\' || "redcap_repeat_instrument" ' \\
'|| \\'"\\' || \\'|\\' || \\'"\\' || "redcap_repeat_instance" ' \\
'|| \\'"\\' || \\'|\\' || \\'"\\' || "ip_order_proc_id_narrative" ' \\
'|| \\'"\\' || \\'|\\' || \\'"\\' || "narrative_line" ' \\
'|| \\'"\\' || \\'|\\' || \\'"\\' || "narrative" ' \\
'|| \\'"\\' || \\'|\\' || \\'"\\' || "imaging_narratives_complete" ' \\
'|| \\'"\\' export ' \\
'from (select distinct rn,"study_id","redcap_repeat_instrument","redcap_repeat_instance"' \\
',"ip_order_proc_id_narrative","narrative_line","narrative","imaging_narratives_complete"' \\
'from xdr_' + v_projid + '_' + v_entcd + '_rc ' \\
'order by "study_id", "redcap_repeat_instance")';
#print(sql)

#------------------------------------------------------------------------------

# End customizations!!!

#------------------------------------------------------------------------------

# Format date

CLdb = Ora.connect('ctsi_research/S5archR3_CTsi@clarityq')
cur = CLdb.cursor()
presql = 'alter session set nls_date_format = \\'MM/DD/YYYY HH24:MI\\'';
cur.execute(presql)
presql = 'select max(rn) into :a from xdr_' + v_projid + '_' + v_entcd + '_rc';
cur.execute(presql)
v_rows = cur.fetchone()
v_max_rows = v_rows\[0\]
print(str('v_max_rows=' + str(v_max_rows)))
v_ctr_min = 1
v_ctr_max = v_limit
v_ctr_file = 1
print(str('v_ctr_min=' + str(v_ctr_min) + ', v_ctr_max=' + str(v_ctr_max)))
fileout = str(filepath + v_file + str(v_ctr_file) + '.csv')

if os.path.exists(fileout):
os.remove(fileout)
writeFile = open(fileout, 'w', encoding='utf8', errors="ignore")
writeFile.write(v_hdr + '\\n')

try:
with CLdb.cursor() as cur:
cur.execute(sql)

while True:
rows = cur.fetchmany(batch_size)
if not rows:
break
for row in rows:
v_col = row\[1\]
v_col = v_col.replace('"', '', 1) # reset first "
v_col = v_col\[:-1\] # remove last "
v_col = v_col.replace('"|"', '|||xxxyyz') # remove double-quotes and change delimiter
v_col = v_col.replace('\\\\', '\\\\\\\\') # escape backslashes
v_col = v_col.replace('\\"', '""') # escape double-quotes
v_col = v_col.replace('|||xxxyyz', '","') # reset all delimiters back to commas
v_col = '"' + v_col # reinstate first "
v_col = v_col + '"' # reinstate last "
if (v_ctr_min <= row\[0\] <= v_ctr_max):
writeFile.write(v_col + '\\n')
else:
writeFile.close()
v_ctr_file = v_ctr_file + 1
fileout = str(filepath + v_file + str(v_ctr_file) + '.csv')
if os.path.exists(fileout):
os.remove(fileout)
writeFile = open(fileout, 'w', encoding='utf8', errors="ignore")
writeFile.write(v_hdr + '\\n')
writeFile.write(v_col + '\\n')
v_ctr_min = v_ctr_max + 1
v_ctr_max = v_ctr_max + v_limit
print(str('v_ctr_min=' + str(v_ctr_min) + ', v_ctr_max=' + str(v_ctr_max)))
except CLdb.Error as error:
print(error)
writeFile.close()
print('ending...')

# Patient Set from I2B2 Query

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 2/6/2023)

– the following gets a patient set from an I2B2 query. Change :i2b2_query_name to your i2b2 query.

SELECT DISTINCT pat.PATIENT_NUM
                    FROM QT_QUERY_MASTER qm
                    JOIN QT_QUERY_INSTANCE qi
                        ON qm.QUERY_MASTER_ID = qi.QUERY_MASTER_ID
                    JOIN QT_QUERY_RESULT_INSTANCE       qr
                        ON qi.QUERY_INSTANCE_ID = qr.QUERY_INSTANCE_ID
                    left JOIN QT_PATIENT_SET_COLLECTION pat
                        ON qr.RESULT_INSTANCE_ID = pat.RESULT_INSTANCE_ID
                    WHERE
                        qr.RESULT_TYPE_ID = 1
                        AND qm.name = :i2b2_query_name
;

# Patient Set from User Patient List in Care Connect

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 9/18/2024)

– the following gets a patient set from an Care Connect based on a user’s Patient List.

select pl.list_id
,pli.list_description
,count(distinct pl.pat_id)
from pat_list pl
join pat_list_info pli on pl.list_id = pli.list_id
where pli.list_creator_id = 'uid' --replace uid with investigator user_id, then ask which is the requested patient list
group by pl.list_id
,pli.list_description
order by pli.list_description
;

# PROMIS Queries

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 3/27/2023)

– the following gets information regarding PROMIS questionnaires

--------------------------------------------------------------------------------

-- Get questionnaires (example-change accordingly per your requirements)

-- • CareConnect Questionnaire Name and ID - EDMONTON SYMPTOM ASSESSMENT SYSTEM \[14010000014\]

-- • CareConnect Questionnaire Name and ID - PROMIS Adult Short Form v 1.0 Physical Function \[1400001701\]

-- • CareConnect Questionnaire Name and ID - PROMIS Adult Short Form v 1.0 Anxiety \[1400001703\]

-- • CareConnect Questionnaire Name and ID - PROMIS Adult Short Form v 1.0 Fatigue \[1400001705\]

-- • CareConnect Questionnaire Name and ID - PROMIS Adult Short Form v 1.0 Sleep Disturbance \[1400001706\]

-- • CareConnect Questionnaire Name and ID - PROMIS Adult Short Form v 1.0 Pain Interference \[1400001708\]

-- • CareConnect Questionnaire Name and ID - PROMIS Adult Short Form v 1.0 Pain Intensity \[1400001709\]

-- • CareConnect Questionnaire Name and ID - PROMIS Adult Short Form v 1.0 Global Health \[1400001710\]

-- • CareConnect Questionnaire MYC UCLA Head and Neck OTV Survey \[140000051\]

-- • CareConnect Questionnaire MYC UCLA Head and Neck PRO-CTCAE \[140000007\]

-- • CareConnect Questionnaire MYC UCLA Head and Neck Survey-CON/FU \[140000010\]

--------------------------------------------------------------------------------

select distinct pefa.pat_id
   ,pefa.pat_enc_csn_id
   ,pefa.qf_lqf_id as form_id
   ,pefa.qf_hqa_id as answer_id
   ,qf.form_name
   ,qf.pat_frndly_name
   ,cqa.quest_id
   ,clqo.question
   ,cqa.line as ans_line
   ,quest_answer
   ,question_instant as answer_time
from pat_enc_form_ans pefa
join cl_qform qf on pefa.qf_lqf_id = qf.form_id
join cl_qanswer_qa cqa on pefa.qf_hqa_id = cqa.answer_id
join cl_qquest cqq on cqa.quest_id = cqq.quest_id -- question name
join cl_qquest_ovtm clqo on cqa.quest_id = clqo.quest_id -- question
where regexp_like(qf.form_name,'+(PROMIS)+','i')
or regexp_like(qf.pat_frndly_name,'+(PROMIS)+','i')
or regexp_like(clqo.question,'+(PROMIS)+','i')
or pefa.qf_lqf_id in (14010000014
,1400001701
,1400001703
,1400001705
,1400001706
,1400001708
,1400001709
,1400001710
,140000051
,140000007
,140000010
)
order by pefa.pat_id, pefa.pat_enc_csn_id, pefa.qf_lqf_id, cqa.line
;

--------------------------------------------------------------------------------

-- Get smartforms

--------------------------------------------------------------------------------

select distinct vo.pat_id
   ,vo.qnr_id as form_id
   ,qf.form_name
   ,qf.pat_frndly_name
   ,cqa.quest_id
   ,clqo.question
   ,vo.qnr_ans_id as answer_id
   ,cqa.line as ans_line
   ,cqa.quest_answer
   ,cqa.question_instant as answer_time
   ,vo.srs_id series_id
   ,si.series_name
from v_pat_reported_outcomes vo
join cl_qanswer_qa cqa on vo.ques_id = cqa.quest_id
and vo.qnr_ans_id = cqa.answer_id
and vo.ques_ans_line = cqa.line
join cl_qform qf on vo.qnr_id = qf.form_id
join cl_qquest cqq on vo.ques_id = cqq.quest_id -- question name
join cl_qquest_ovtm clqo on vo.ques_id = clqo.quest_id -- question
join srs_info si on vo.srs_id = si.series_id
where regexp_like(si.series_name,'+(PROMIS)+','i')
or regexp_like(qf.form_name,'+(PROMIS)+','i')
or regexp_like(qf.pat_frndly_name,'+(PROMIS)+','i')
or regexp_like(clqo.question,'+(PROMIS)+','i')
;

--------------------------------------------------------------------------------

-- Other potential findings

--------------------------------------------------------------------------------

select distinct pefa.pat_enc_csn_id
   ,pefa.pat_id
   ,pefa.contact_date
   ,pefa.qf_lqf_id
   ,pefa.qf_hqa_id
   ,pefa.qf_status_c
   ,qf.form_name
   ,cqa.answer_id
   ,cqa.line
   ,cqa.quest_id
   ,cqa.quest_dat
   ,cqa.quest_answer
   ,cqa.question_instant
   ,cqa.float_answer
   ,cqa.numeric_answer
   ,cqa.varchar_answer
   ,cqq.quest_name
   ,clqo.question
   ,pefa.qf_cosign_emp_id
   ,case when coh.pat_id is null then 1 else 0 end pat_in_lz_tbl_yn
from pat_enc_form_ans pefa
left join i2b2.lz_clarity_patient coh on pefa.pat_id = coh.pat_id
left join cl_qform qf on pefa.qf_lqf_id = qf.form_id
left join cl_qanswer_qa cqa on pefa.qf_hqa_id = cqa.answer_id
left join cl_qquest cqq on cqa.quest_id = cqq.quest_id -- question name
left join cl_qquest_ovtm clqo on cqa.quest_id = clqo.quest_id -- question
where pefa.qf_lqf_id in (14010000014 --change according to your requirements
,1400001701
,1400001703
)
;

# Jira Billing Hours

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 6/26/2023)

– The following gets billable Jira hours that should be reviewed further for accuracy prior to delivering to consultant group for billing. These exclude “do not bill” hours.

Steps:

1.  Run a Jira Tempo report for your projects to date. Below are the parameters for the report.
2.  ![](data:image/png;base64,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)

Export results. Remove first column prior to importing.

1.  Import results to ctsi_research.xdr_xxbill_all, where xx are your initials. TT is used as the example below.
    1.  Ensure data size is large enough to accept all values (e.g. issue=varchar(2000), worklog=varchar(4000)).
    2.  Rename DATE to dt.
2.  Run the following script.

--------------------------------------------------------------------------------

-- Steps:

-- 1. Run Jira TT_Billing_All report for all - DELETE first COLUMN
-- 2. Import #1 results to xdr_ttbill_all

--------------------------------------------------------------------------------

drop table xdr_ttbill_all purge;

select * from xdr_ttbill_all order by key desc;
--
drop table xdr_ttbill_fin purge;

create table xdr_ttbill_fin as
select distinct 'q' tp
,key jira_key
,issue jira_title
,sum(logged) over (partition by key) logged
from xdr_ttbill_all c
where nvl(key,'TOTAL') like 'XDR%'
and worklog is not null
and do_not_bill_these_hours = 'false'
and to_date(substr(dt,1,9),'dd/mon/yy') between to_date('04/01/2023','mm/dd/yyyy') and sysdate --change to reflect current billable quarter
union
select distinct 'a' tp
,key jira_key
,issue jira_title
,sum(logged) over (partition by key) logged
from xdr_ttbill_all c
where nvl(key,'TOTAL') like 'XDR%'
and worklog is not null
and do_not_bill_these_hours = 'false'
order by jira_key
;

select distinct c.jira_key
,c.jira_title
,c.logged currqtr_logged
,a.logged all_logged
from xdr_ttbill_fin c
join xdr_ttbill_fin a on c.jira_key = a.jira_key and a.tp = 'a'
where c.tp = 'q'
and a.logged > 20
order by c.jira_key
;

# Cancer Registrar Datamart METS

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 6/27/2023)

– The following gets determines metastatic cancer (Use I:\\_BIP\\xDR\\Cancer registry integration\\Documentation_asof_12222022\\Code_Library.sql for preliminary tables)
-- Check for mets
--Pre-2018 (#Temp123456CSTG1)

select *
from cancerregistrardm.rpt.cr_codelookupdim zcmdlu
where zcmdlu.fieldname in ('CS Mets at Dx-Bone','CS Mets at Dx-Brain','CS Mets at Dx-Liver','CS Mets at Dx-Lung') --If value = '1', it's METS
order by fieldname, lookupcode
;

select distinct
CS_MetsAtDiagnosis --Where can I find meaningful values for these? --ASSUMING '10' and '70' as METS
,CS_MetsEval --Where can I find meaningful values for these? --ASSUMING ='1' as METS
from #Temp123456CSTG1
where CS_MetsAtDiagnosis is not null
and CS_MetsEval is not null

--order by ip_patient_id
;

--Post-2018 (#Temp123456CSTG2)

select * from cancerregistrardm.rpt.cr_codelookupdim zcmdlu
where zcmdlu.fieldname in ('Mets at DX, Bone','Mets at DX, Brain','Mets at DX, Liver','Mets at DX, Lung','Mets at DX, Other'
,'Summary Stage 2018','EOD Primary Tumor','EOD Regional Nodes','EOD Mets')
order by fieldname, lookupcode
;

--METS assumptions:
--mets at dx = '1'
--eod mets between '10' and '70'
--eod primary tumor between '050' and '750'
--eod regional nodes between '050' and '750'
--summary stage 2018 between '1' and '8' --'8'=BENIGN OR BORDERLINE BRAIN???

# Discharge Location

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 2/27/2024)

– The following determines to what general location a patient was discharged.

drop table xdr_123456_cohex_disch purge;

create table xdr_123456_cohex_disch as
select distinct b.pat_id
   ,b.pat_enc_csn_id
   ,B.DISCH_DISP_C
   ,disp.name as DISCH_DISP_C_name
   ,B.DISCH_DEST_C
   ,dest.name as DISCH_DEST_C_name
   ,b.ed_disposition_c
   ,edd.name ed_disposition
   ,zps.name as adt_pat_status
   ,b.admit_conf_stat_c
   ,zcs.name as conf_stat
FROM xdr_123456_cohpat a
join PAT_ENC_HSP B on a.pat_id = b.pat_id
LEFT OUTER JOIN ZC_PAT_CLASS H ON B.ADT_PAT_CLASS_C = H.ADT_PAT_CLASS_C
left outer join ZC_DISCH_DISP disp on disp.DISCH_DISP_C = B.DISCH_DISP_C
left outer join ZC_DISCH_DEST dest on dest.DISCH_DEST_C = B.DISCH_DEST_C
LEFT JOIN zc_ed_disposition edd ON b.ed_disposition_c = edd.ed_disposition_c
left join ZC_PAT_STATUS zps on b.ADT_PATIENT_STAT_C = zps.ADT_PATIENT_STAT_C
left join ZC_conf_stat zcs on b.admit_conf_stat_c = zcs.admit_conf_stat_c
--below where clause is customizable per requirements...
--where regexp_like(nvl(disp.name,'xxx'),'+(hospice|jail|prison|psych|ama|against med|another facility)+','i')
-- or regexp_like(nvl(dest.name,'xxx'),'+(hospice|jail|prison|psych|ama|against med|another facility)+','i')
-- or regexp_like(nvl(edd.name,'xxx'),'+(hospice|jail|prison|psych|ama|against med|another facility)+','i')
;

create index xdr_123456_cohex_disch_patidx on xdr_123456_cohex_disch (pat_id);

select count(*), count(distinct pat_id) from xdr_123456_cohex_disch;

select * from xdr_123456_cohex_disch order by pat_id;

# Sex Assigned at Birth

by [Cenan Pirani](https://uclabip.atlassian.net/wiki/people/5f5922bcff2fe2006fb700d8?ref=confluence) on 4/5/2024

*   The following gets the sex assigned at birth for a cohort

select distinct coh.pat_id
    ,s.name sex_assigned_at_birth
from xdr_prinv_coh coh
join patient_4 p on coh.pat_id = p.pat_id
left join zc_sex_asgn_at_birth s on p.sex_asgn_at_birth_c = s.sex_asgn_at_birth_c
;

# Historical Addresses in MSSQL

by [Cenan Pirani](https://uclabip.atlassian.net/wiki/people/5f5922bcff2fe2006fb700d8?ref=confluence) on 8/29/2024

The database and table to access all historical address information (tracking address changes of patients) can be found in ConfomedDims.dim_RI.PatientDimHistory

Below is an example:

![](data:image/png;base64,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)

# PDF Scans from Clarity

by [Cenan Pirani](https://uclabip.atlassian.net/wiki/people/5f5922bcff2fe2006fb700d8?ref=confluence) on 9/16/2024

Below is the SQL code to retrieve PDF scanned documents for a cohort:

drop table xdr_prinv_docs purge;

create table xdr_prinv_docs as

select distinct coh.pat_id
,bb.doc_info_id
,bb.doc_recv_time
,bb.SCAN_FILE
,bb.DOC_DESCR
,bb.DOC_INFO_TYPE_C
,bt.name as doc_type
--,BT.DOC_GROUP
from xdr_prinv_coh coh
join DOC_INFORMATION bb on bb.doc_pt_id = coh.pat_id
left join ZC_DOC_INFO_TYPE bt on BB.DOC_INFO_TYPE_C = BT.DOC_INFO_TYPE_C
WHERE BB.IS_SCANNED_YN = 'Y'
and (bb.RECORD_STATE_C <> 2 OR bb.RECORD_STATE_C IS NULL) -- Deleted
and (bb.DOC_STAT_C <> 35 OR bb.DOC_STAT_C IS NULL) -- Error
and bb.DOC_REVOK_DT is null
;

Once this is created pull the list of files names into a CSV with the query:

select distinct scan_file from xdr_prinv_docs;

Share the file with and notify Wen Ting ([wting@mednet.ucla.edu](mailto:wting@mednet.ucla.edu)), he is the one person that will pull the PDFs and place them in the same share Box.

Use the following for the Subject line and Box folder name: **PODS document request: IRB ##-####: Title - PI**

# PHQ9, PROMIS, SDOH, GAD7, AUDITC

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 3/18/2025)

– The following obtains data for PHQ9, PROMIS, SDOH, GAD7, AUDITC. When investigators ask for these, developers can deliver flowsheets and/or questionnaires.

----------------------------------------------------------------------------------------------

-- Use i2b2.bip_driver

----------------------------------------------------------------------------------------------

--The following DDL defines how this driver was created.

drop table i2b2.bip_driver purge;

--create table i2b2.bip_driver as
--select distinct 'FLOWSHEET' driver_source
-- ,'IP_FLO_GP_DATA.FLO_MEAS_ID' driver_column
-- ,case
-- when regexp_like(gpd.flo_meas_name,'+(sdoh|soc.+det)+','i') then 'SDOH'
-- when regexp_like(gpd.flo_meas_name,'+(phq.+9)+','i') then 'PHQ9'
-- when regexp_like(gpd.flo_meas_name,'+(promis)+','i') then 'PROMIS'
-- when regexp_like(gpd.flo_meas_name,'+(GAD7)+','i') then 'GAD7'
-- when regexp_like(gpd.flo_meas_name,'+(AUDITC)+','i') then 'AUDITC'
-- end driver_type
-- ,gpd.flo_meas_id driver_id
-- ,gpd.flo_meas_name driver_name
-- FROM ip_flo_gp_data gpd
-- where regexp_like(flo_meas_name,'+(sdoh|phq.+9|promis|gad7|auditc)+','i')
--union
--select distinct 'QUESTIONNAIRE_FORM' driver_source
-- ,'CL_QFORM.FORM_ID' driver_column
-- ,case
-- when regexp_like(f.form_name,'+(sdoh|soc.+det)+','i') then 'SDOH'
-- when regexp_like(f.form_name,'+(phq.+9)+','i') then 'PHQ9'
-- when regexp_like(f.form_name,'+(promis)+','i') then 'PROMIS'
-- when regexp_like(f.form_name,'+(GAD7)+','i') then 'GAD7'
-- when regexp_like(f.form_name,'+(AUDITC)+','i') then 'AUDITC'
-- end driver_type
-- ,f.form_id driver_id
-- ,f.form_name driver_name
-- from CL_QFORM f
-- where regexp_like(f.form_name,'+(sdoh|phq.+9|promis|gad7|auditc)+','i')
--union
--select distinct 'QUESTIONNAIRE_QUESTION' driver_source
-- ,'CL_QQUEST.QUEST_NAME' driver_column
-- ,case
-- when regexp_like(quest_name,'+(sdoh|soc.+det)+','i') then 'SDOH'
-- when regexp_like(quest_name,'+(phq.+9)+','i') then 'PHQ9'
-- when regexp_like(quest_name,'+(promis)+','i') then 'PROMIS'
-- when regexp_like(quest_name,'+(GAD7)+','i') then 'GAD7'
-- when regexp_like(quest_name,'+(AUDITC)+','i') then 'AUDITC'
-- end driver_type
-- ,quest_id driver_id
-- ,quest_name driver_name
-- from CL_QQUEST
-- where regexp_like(quest_name,'+(sdoh|phq.+9|promis|gad7|auditc)+','i')
-- order by driver_source, driver_type, driver_id
--;

--alter table bip_driver add constraint bip_driver_pk primary key (driver_source, driver_type, driver_id);

--

--insert into i2b2.bip_driver (driver_source, driver_column, driver_type, driver_id, driver_name)
--select distinct 'FLOWSHEET','IP_FLO_GP_DATA.FLO_MEAS_ID','SDOH',gpd.flo_meas_id, gpd.flo_meas_name
-- from ctsi_research.lz_flowsheet_sdoh_icl_zc js --this is from JS SDOH subitem from Kruse 23-5190 (Project_id=115824)
-- join ip_flo_gp_data gpd on js.flowsheetrowid = gpd.flo_meas_id
-- left join i2b2.bip_driver bd on js.flowsheetrowid = bd.driver_id and bd.driver_source = 'FLOWSHEET' and bd.driver_type = 'SDOH'
-- where bd.driver_id is null
--; --210 rows inserted

--commit;

----------------------------------------------------------------------------------------------

-- Using the standard bip driver, get flowsheets and questionnaires data.

----------------------------------------------------------------------------------------------

-- Flowsheets

SELECT DISTINCT coh.pat_id
   ,enc.pat_enc_csn_id
   ,enc.inpatient_data_id
   ,rec.fsd_id
   ,meas.flt_id
   ,meas.flo_meas_id
   ,dta.display_name
   ,dta.template_name
   ,gpd.disp_name measure_name
   ,gpd.flo_meas_name
   ,meas.meas_value
   ,meas.recorded_time
   ,MAX(meas.recorded_time) OVER (PARTITION BY enc.pat_id, meas.flo_meas_id) latest_measure
FROM xdr_123456_coh coh
JOIN i2b2.lz_clarity_enc enc ON coh.pat_id = enc.pat_id
JOIN ip_flwsht_rec rec ON enc.inpatient_data_id = rec.inpatient_data_id
JOIN ip_flwsht_meas meas ON rec.fsd_id = meas.fsd_id
JOIN ip_flo_gp_data gpd ON meas.flo_meas_id = gpd.flo_meas_id
JOIN ip_flt_data dta ON meas.flt_id = dta.template_id
JOIN i2b2.bip_driver fd on meas.flo_meas_id = fd.driver_id
and fd.driver_source = 'FLOWSHEET'
and fd.driver_type in ('SDOH','PHQ9','PROMIS','GAD7') --Change according to your requirements
--NOTE: For standard SDOH pulls, use 'SDOH' only unless specifically requested by PI
WHERE meas.recorded_time IS NOT NULL
AND meas.meas_value IS NOT NULL
;

-- Questionnaires

select distinct coh.pat_id
   ,f.form_id
   ,f.form_name
   ,q.quest_id
   ,q.quest_name
   ,qa.answer_id
   ,qa.line
   ,qa.quest_answer
   ,qa.quest_dat datetime_answer
from xdr_123456_coh coh
join CL_QANSWER a on coh.pat_id = a.pat_id
join CL_QANSWER_QA qa on a.answer_id = qa.answer_id
join CL_QQUEST q on qa.quest_id = q.quest_id
join CL_QFORM_QUEST fq on q.quest_id = fq.quest_id
join CL_QFORM f on fq.form_id = f.form_id
join i2b2.bip_driver qd on (fq.form_id = qd.driver_id
and qd.driver_source = 'QUESTIONNAIRE_FORM'
and qd.driver_type in ('SDOH','PHQ9','PROMIS','GAD7') --Change according to your requirements
--NOTE: For standard SDOH pulls, use 'SDOH' only unless specifically requested by PI
)
or
(fq.quest_id = qd.driver_id
and qd.driver_source = 'QUESTIONNAIRE_QUESTION'
and qd.driver_type in ('SDOH','PHQ9','PROMIS','GAD7') --Change according to your requirements
--NOTE: For standard SDOH pulls, use 'SDOH' only unless specifically requested by PI
)
;

-- Combine flowsheet and questionnaire results

select x.* from (
select pat_id
,'QUESTIONNAIRE' src
, form_name form_or_template_name
, quest_name question_or_display_name
, null measure_name
, to_char(line) answer_line_or_flow_measure
, quest_answer val
, datetime_answer dt
from xdr_123456_quest
union
select pat_id
,'FLOWSHEET' src
, template_name form_or_template_name
, display_name question_or_display_name
, measure_name measure_name
, vital_sign_type answer_line_or_flow_measure
, vital_sign_value val
, to_date(vital_sign_taken_time) dt
from xdr_123456_flow
) x
order by ip_patient_id, sdoh_date
;

----------------------------------------------------------------------------------------------

-- For additional SDOH (Social Determinants of Health), developers can query Social History.
-- The following obtains SDOH data using Social History.
-- Many times, social hx does not contain all the information requested for SDOH,
-- so flowsheets/questionnaires should also be queried.

----------------------------------------------------------------------------------------------

select distinct coh.pat_id, coh.study_id
   ,soc.contact_date
   ,xedu.name education_level
   ,zfrs.name financial_res_strain
   ,ziea.name ipv_emotional_abuse
   ,zfear.name ipv_fear
   ,zisa.name ipv_sexual_abuse
   ,zipa.name ipv_physical
   ,zaf.name alcohol_freq
   ,zadpd.name alcohol_drinks_p_day
   ,zbinge.name alcohol_binge
   ,zspouse.name living_w_spouse
   ,zstress.name daily_stress
   ,zphone.name phone_communication
   ,zsocial.name socialize_freq
   ,zchurch.name church_attendance
   ,zclub.name club_mtg_attend
   ,zclubm.name club_member
   ,zpdpw.name phys_act_days_p_wk
   ,zpamps.name phys_act_min_p_sess
   ,zfoods.name food_insec_scarce
   ,zfoodw.name food_insec_worry
   ,zmedtr.name med_trans_needs
   ,zothertr.name other_trans_needs
from p_coh coh
join social_hx soc on coh.pat_id = soc.pat_id
left join zc_edu_level xedu on soc.edu_level_c = xedu.edu_level_c
left join zc_fin_resource_strain zfrs on soc.fin_resource_strain_c = zfrs.fin_resource_strain_c
left join zc_ipv_emotional_abuse ziea on soc.ipv_emotional_abuse_c = ziea.ipv_emotional_abuse_c
left join zc_ipv_fear zfear on soc.ipv_fear_c = zfear.ipv_fear_c
left join zc_ipv_sexual_abuse zisa on soc.ipv_sexual_abuse_c = zisa.ipv_sexual_abuse_c
left join zc_ipv_physical_abuse zipa on soc.ipv_physical_abuse_c = zipa.ipv_physical_abuse_c
left join ZC_ALCOHOL_FREQ zaf on soc.ALCOHOL_FREQ_C = zaf.ALCOHOL_FREQ_C
left join zc_alcohol_drinks_per_day zadpd on soc.alcohol_drinks_per_day_c = zadpd.alcohol_drinks_per_day_c
left join zc_alcohol_binge zbinge on soc.alcohol_binge_c = zbinge.alcohol_binge_c
left join zc_living_w_spouse zspouse on soc.living_w_spouse_c = zspouse.living_w_spouse_c
left join zc_daily_stress zstress on soc.daily_stress_c = zstress.daily_stress_c
left join zc_phone_communication zphone on soc.phone_communication_c = zphone.phone_communication_c
left join zc_socialization_freq zsocial on soc.socialization_freq_c = zsocial.socialization_freq_c
left join zc_church_attendance zchurch on soc.church_attendance_c = zchurch.sdoh_church_attendance_c
left join zc_clubmtg_attendance zclub on soc.clubmtg_attendance_c = zclub.clubmtg_attendance_c
left join zc_club_member zclubm on soc.club_member_c = zclubm.club_member_c
left join zc_phys_act_days_per_week zpdpw on soc.phys_act_days_per_week_c = zpdpw.phys_act_days_per_week_c
left join zc_phys_act_min_per_sess zpamps on soc.phys_act_min_per_sess_c = zpamps.phys_act_min_per_sess_c
left join zc_food_insecurity_scarce zfoods on soc.food_insecurity_scarce_c = zfoods.food_insecurity_scarce_c
left join zc_food_insecurity_worry zfoodw on soc.food_insecurity_worry_c = zfoodw.food_insecurity_worry_c
left join zc_med_transport_needs zmedtr on soc.med_transport_needs_c = zmedtr.med_transport_needs_c
left join zc_other_transport_needs zothertr on soc.other_transport_needs_c = zothertr.other_transport_needs_c
WHERE soc.pat_enc_date_real = (SELECT MAX(soc.pat_enc_date_real) FROM social_hx soc WHERE soc.pat_id = coh.pat_id)
and (soc.edu_level_c is not null
or soc.fin_resource_strain_c is not null
or soc.ipv_emotional_abuse_c is not null
or soc.ipv_fear_c is not null
or soc.ipv_sexual_abuse_c is not null
or oc.ipv_physical_abuse_c is not null
or soc.ALCOHOL_FREQ_C is not null
or soc.alcohol_drinks_per_day_c is not null
or soc.alcohol_binge_c is not null
or soc.living_w_spouse_c is not null
or soc.daily_stress_c is not null
or soc.phone_communication_c is not null
or soc.socialization_freq_c is not null
or soc.church_attendance_c is not null
or soc.clubmtg_attendance_c is not null
or soc.club_member_c is not null
or soc.phys_act_days_per_week_c is not null
or soc.phys_act_min_per_sess_c is not null
or soc.food_insecurity_scarce_c is not null
or soc.food_insecurity_worry_c is not null
or soc.med_transport_needs_c is not null
or soc.other_transport_needs_c is not null
)
;

# Dispensed Medications

by [Cenan Pirani](https://uclabip.atlassian.net/wiki/people/5f5922bcff2fe2006fb700d8?ref=confluence) on 9/30/2025

-- STAGE TABLE

DROP TABLE XDR_PID_MEDFILLALL PURGE;

CREATE TABLE XDR_PID_MEDFILLALL AS
select distinct docs.pat_id
   ,ip_patient_id
   ,disp.EXT_MED_ERX_ID
   ,disp.EXT_DRUG_DISP_INST_DTTM
   ,disp.EXT_DRUG_DESP
   ,disp.EXT_DRUG_DISP_AMT
   ,disp.EXT_DRUG_DISP_UNIT
   ,disp.EXT_DRUG_DOSE_FORM
   ,disp.EXT_MED_DAY_SUPPLY
   --,docs.TYPE_C
   ,disp.EXT_DRUG_PROV_NAME
FROM xdr_PID_pat coh
JOIN DOCS_RCVD docs on docs.pat_id = coh.pat_id
JOIN MED_DISPENSE disp on docs.document_id = disp.document_id
;

-- PULL

select distinct ip_patient_id
   ,EXT_MED_ERX_ID
   ,to_char(EXT_DRUG_DISP_INST_DTTM, 'mm/dd/yyyy hh24:mi') EXT_DRUG_DISP_INST_DTTM
   ,EXT_DRUG_DESP
   ,EXT_DRUG_DISP_AMT
   ,EXT_DRUG_DISP_UNIT
   ,EXT_DRUG_DOSE_FORM
   ,EXT_MED_DAY_SUPPLY
   ,EXT_DRUG_PROV_NAME
from XDR_PID_MEDFILLALL
;

# Referrals

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 7/17/2026)
select distinct cpo.study_id ip_patient_id
               ,r.referral_id
               ,r.ENTRY_DATE
               ,rg.prov_name referring_provider
               ,rl.prov_name referral_provider
               ,dep.department_name
               ,dep.specialty
               ,zp.name priority
               ,r.AUTH_NUM_OF_VISITS
               ,r.SCHED_NUM_VISITS	
               ,r.REQUEST_NUM_VISITS
               ,zc.name class
               ,zrsn.name reason
  from xdr_prinv_coh cpo
  join referral r on cpo.pat_id = r.pat_id
  left join clarity_ser rg on r.referring_prov_id = rg.prov_id
  left join clarity_ser rl on r.referral_prov_id = rl.prov_id
  left join clarity_dep dep on r.REFD_TO_DEPT_ID = dep.department_id
  left join zc_specialty zs on r.refd_to_spec_c = zs.specialty_c
  left join ZC_RFL_PRIORITY zp on r.priority_c = zp.priority_c
  left join zC_RFL_CLASS zc on r.RFL_CLASS_C = zc.RFL_CLASS_C
  left join zC_RSN_FOR_RFL zrsn on r.RSN_FOR_RFL_C = zrsn.RSN_FOR_RFL_C
  order by study_id
;
