# Useful Queries and Other Resources

*   [AD-POLST](#AD-POLST)
*   [Calculate Time on a Ventilator](#Calculate-Time-on-a-Ventilator)
*   [Health Maintenance Report](#[Health-Maintenance-Report)
*   [Orders for Inpatient Transfers](#Orders-for-Inpatient-Transfers)
*   [BMT (Bone Marrow Transplant)](#UsefulQueriesandOtherResources-BMT\(Bone)
*   [MyChart Activity](#UsefulQueriesandOtherResources-MyChartA)
    *   [MyChart Session Information (from EPIC docu)](#UsefulQueriesandOtherResources-MyChartS)
    *   [MyChart Patient Status](#UsefulQueriesandOtherResources-MyChartP)
*   [DEATH TABLE](#DEATH-TABLE)
*   [Face to Face Visits](#UsefulQueriesandOtherResources-FacetoFa)
*   [Chief Complaints](#UsefulQueriesandOtherResources-ChiefCom)
*   [Current Med List](#UsefulQueriesandOtherResources-CurrentM)
*   [Multiple Phone Numbers and Patient's preference](#UsefulQueriesandOtherResources-Multiple)
*   [Beaker Text Reports (narratives, etc...)](#UsefulQueriesandOtherResources-BeakerTe)
*   [Check Patient's Contact Information](#UsefulQueriesandOtherResources-CheckPat)
*   [All Flowsheet Questions on a Template](#UsefulQueriesandOtherResources-AllFlows)
*   [Past Medical History](#UsefulQueriesandOtherResources-PastMedi)
*   [Procedure Performing and Billing Providers](#UsefulQueriesandOtherResources-Procedur)
*   [Database Cleanup/Queries](#UsefulQueriesandOtherResources-Database)
*   [Care Everywhere Labs](#Care-Everywhere-Labs)
*   [Occupation Information](#UsefulQueriesandOtherResources-Occupati)
    *   [Occupation](#UsefulQueriesandOtherResources-Occupati)
*   [Health Maintenance Topics](#UsefulQueriesandOtherResources-HealthMa)
*   [Questionnaires and Answers](#UsefulQueriesandOtherResources-Question)
*   [Address History (includes homeless)](#UsefulQueriesandOtherResources-AddressH)
*   [Cause of Death](#UsefulQueriesandOtherResources-CauseofD)
*   [GFR labs](#UsefulQueriesandOtherResources-GFRlabs)
*   [Kill Sessions](#UsefulQueriesandOtherResources-KillSess)
*   [Locked Objects](#UsefulQueriesandOtherResources-LockedOb)
*   [Recruitment Request Prioritization](#UsefulQueriesandOtherResources-Recruitm)
*   [Bone Marrow Transplant (BMT)](#UsefulQueriesandOtherResources-BoneMarr)
*   [Pregnancy/Delivery](#UsefulQueriesandOtherResources-Pregnanc)
*   [Calculate ICU stay times](#UsefulQueriesandOtherResources-Calculat)
*   [Glasgow Comma Score (GCS)](#UsefulQueriesandOtherResources-GlasgowC)
*   [Sequential Organ Failure Assessment (SOFA)](#UsefulQueriesandOtherResources-Sequenti)
*   [DENSE RANK in Oracle](#UsefulQueriesandOtherResources-DENSERAN)
    *   [How to pull first value in a query based on order rank](#UsefulQueriesandOtherResources-Howtopul)
*   [Pathology HonestBroker Accession Numbers](#UsefulQueriesandOtherResources-Patholog)
*   [Imaging HonestBroker Accession Numbers](#UsefulQueriesandOtherResources-ImagingH)
*   [Changing IDs and MRNs](#UsefulQueriesandOtherResources-Changing)
*   [Control Cohort Identification](#UsefulQueriesandOtherResources-ControlC)
*   [Vaccination](#UsefulQueriesandOtherResources-Vaccinat)
*   [ICU Care](#UsefulQueriesandOtherResources-ICUCare)
*   [SmartData Elements](#UsefulQueriesandOtherResources-SmartDat)
*   [Birth Control / Contraception / Sexually Active](#UsefulQueriesandOtherResources-BirthCon)
*   [PHI disclosure HHS Guidelines](#UsefulQueriesandOtherResources-PHIdiscl)
*   [Provider Email](#UsefulQueriesandOtherResources-Provider)
*   [ICD-10-CM Updates to Lookup Table](#UsefulQueriesandOtherResources-ICD-10-C)
*   [Escape special characters in large texts](#UsefulQueriesandOtherResources-Escapesp)
*   [Splitting table data into smaller files](#UsefulQueriesandOtherResources-Splittin)
*   [Patient Set from I2B2 Query](#UsefulQueriesandOtherResources-PatientS)
*   [Patient Set from User Patient List in Care Connect](#UsefulQueriesandOtherResources-PatientS)
*   [PROMIS Queries](#UsefulQueriesandOtherResources-PROMISQu)
*   [Jira Billing Hours](#UsefulQueriesandOtherResources-JiraBill)
*   [Cancer Registrar Datamart METS](#UsefulQueriesandOtherResources-CancerRe)
*   [Discharge Location](#UsefulQueriesandOtherResources-Discharg)
*   [Sex Assigned at Birth](#UsefulQueriesandOtherResources-SexAssig)
*   [Historical Addresses in MSSQL](#UsefulQueriesandOtherResources-Historic)
*   [PDF Scans from Clarity](#UsefulQueriesandOtherResources-PDFScans)
*   [PHQ9, PROMIS, SDOH, GAD7, AUDITC](#UsefulQueriesandOtherResources-PHQ9,PRO)
*   [Dispensed Medications](#UsefulQueriesandOtherResources-Dispense)

## AD-POLST

###### (by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 6/26/19)

The following code identified the AD/POLST patient records from the scanned documents. It starts by creating a driver list, classifying it, pulling the scanned docs, and exporting them.

```sql
/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

AD-POLST

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/

\-------------------------------------------

\--Step 1: Create driver table for the AD/POLST doc types with final set of codes (use your own codes)

\-------------------------------------------

DROP TABLE XDR\_MEHTA\_ADPOLST\_DOCTYPE PURGE;

CREATE TABLE XDR\_MEHTA\_ADPOLST\_DOCTYPE AS

SELECT \*

FROM ZC\_DOC\_INFO\_TYPE

where DOC\_INFO\_TYPE\_C in (

'11' -- Power of Attorney

,'300052' --Advance Directive Enduring

,'10' --Advance Directives and Living Will

,'200068' --DNR (Do Not Resuscitate) Documentation

,'300058' ---POLST

);

\-------------------------------------------

\--Step 2: Classify documents into AD or POLST category

\-- Upon revision of your selection, your must group the different docs into these two categories

\-------------------------------------------

ALTER TABLE XDR\_MEHTA\_ADPOLST\_DOCTYPE ADD DOC\_GROUP VARCHAR2(10);

UPDATE XDR\_MEHTA\_ADPOLST\_DOCTYPE

SET DOC\_GROUP = 'POLST'

WHERE

DOC\_INFO\_TYPE\_C IN ('200068' -- DNR (Do Not Resuscitate) Documentation

,'300058' -- POLST);

);

COMMIT;

UPDATE XDR\_MEHTA\_ADPOLST\_DOCTYPE

SET DOC\_GROUP = 'AD'

WHERE

DOC\_INFO\_TYPE\_C in (

'11' -- Power of Attorney

,'300052' --Advance Directive Enduring

,'10' --Advance Directives and Living Will

);

COMMIT;

\-------------------------------------------

\-- Step 3: Pull all scanned docs

\--

\-------------------------------------------

DROP TABLE xdr\_MEHTA\_scan\_docs PURGE;

CREATE TABLE xdr\_MEHTA\_scan\_docs AS

SELECT distinct coh.pat\_id

,coh.study\_id

,bb.doc\_info\_id

,bb.doc\_recv\_time

,bb.SCAN\_FILE

,bt.name as doc\_type

,BT.DOC\_GROUP

FROM xdr\_MEHTA\_PAT COH

join DOC\_INFORMATION BB on coh.PAT\_ID = BB.DOC\_PT\_ID

join XDR\_MEHTA\_ADPOLST\_DOCTYPE BT on BB.DOC\_INFO\_TYPE\_C = BT.DOC\_INFO\_TYPE\_C

WHERE

BB.IS\_SCANNED\_YN = 'Y'

\--and bb.doc\_recv\_time between sysdate - (365.25 \*3 ) AND sysdate --(last three years);

\-- We noticed that in the case of AD/POLST documents, there were instances where the docs had been deleted

and (bb.RECORD\_STATE\_C <> 2 OR bb.RECORD\_STATE\_C IS NULL)-- Deleted

and (bb.DOC\_STAT\_C <> 35 OR bb.DOC\_STAT\_C IS NULL)-- Error

and bb.DOC\_REVOK\_DT is null

\-- and bb.DOC\_EXPIR\_TIME is null

;

SELECT COUNT(\*), COUNT(DISTINCT PAT\_ID) FROM xdr\_MEHTA\_scan\_docs;--179 110

\-------------------------------------------

\-- Step 4: Export

\--

\-------------------------------------------

SELECT DISTINCT PAT\_ID

,STUDY\_ID

,doc\_recv\_time

,DOC\_GROUP

FROM xdr\_MEHTA\_scan\_docs

WHERE doc\_recv\_time IS NOT NULL ;
```
## Calculate Time on a Ventilator

###### (by [Robert Follett (Unlicensed)](https://uclabip.atlassian.net/wiki/people/557058:fb90de8e-f6e8-4a11-b471-2992e0b019af?ref=confluence) on 11/06/2020)

This code will show you how to calculate ventilator time in days for a hospital stay.

The official method and MS SQL code sample is kept in Collibra here: [https://uclahealth.collibra.com/asset/cf559613-9b4d-4e73-831a-9429d41d83ef](https://uclahealth.collibra.com/asset/cf559613-9b4d-4e73-831a-9429d41d83ef)

```sql
/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

For Ventilator data, restrict to Airway LDAs

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/

create table xdr\_PROJECT\_airway\_ids as

SELECT t.ID,

t.CONTACT\_DATE\_REAL,

gp.FLO\_MEAS\_NAME as LDA\_NAME

FROM IP\_FLO\_LDA\_TYPES t

JOIN IP\_FLO\_GP\_DATA gp ON t.ID = gp.FLO\_MEAS\_ID

WHERE t.LDA\_TYPE\_OT\_C = '11'; -- per Vihn Wells, validated by Dr. Neil Parikh

/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

Get all Airway LDAs associated with discharged inpatient encounters

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/

drop table xdr\_PROJECT\_airways purge;

create table xdr\_PROJECT\_airways as

SELECT ip\_patient\_id

,ip\_enc\_id

,enc.pat\_enc\_csn\_id

,clenc.inpatient\_data\_id

,florow.LINE

,lda.IP\_LDA\_ID

,lda.FLO\_MEAS\_ID as LDA\_FLO\_MEAS\_ID

,lda.PLACEMENT\_INSTANT

,lda.REMOVAL\_DTTM

,air.LDA\_NAME

,enc.hosp\_admsn\_time

,enc.hosp\_dischrg\_time

FROM xdr\_PROJECT\_enc enc

join i2b2.lz\_clarity\_enc clenc on enc.pat\_enc\_csn\_id = clenc.pat\_enc\_csn\_id

JOIN IP\_FLOWSHEET\_ROWS florow ON clenc.Inpatient\_Data\_ID = florow.INPATIENT\_DATA\_ID

AND florow.IP\_LDA\_ID IS NOT NULL

JOIN IP\_LDA\_NOADDSINGLE lda ON florow.IP\_LDA\_ID = lda.IP\_LDA\_ID

JOIN xdr\_PROJECT\_airway\_ids air ON lda.FLO\_MEAS\_ID = air.ID

AND lda.LDA\_GROUP\_CDR = air.CONTACT\_DATE\_REAL

;

/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

Because Placement and Removal data is unreliable (manual entry), pull RecordedTime from assessment rows

\- exclude LDAs with no assessment rows during the discharge encounter

\- exclude assessment rows with RecordedTime before admission or after discharge

\- exclude assessment rows before Placement, when that is documented

\- exclude assessment rows after Removal, when that is documented

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/

create table xdr\_PROJECT\_air\_meas as

SELECT ip\_patient\_id,

ip\_enc\_id,

hosp\_admsn\_time,

hosp\_dischrg\_time,

ldarows.LDA\_FLO\_MEAS\_ID,

ldarows.LDA\_NAME,

ldarows.IP\_LDA\_ID,

ldarows.PLACEMENT\_INSTANT,

ldarows.REMOVAL\_DTTM,

ldarows.INPATIENT\_DATA\_ID,

ldarows.LINE as RowLine,

flwrec.FSD\_ID,

meas.LINE,

meas.FLO\_MEAS\_ID,

meas.RECORDED\_TIME,

meas.MEAS\_VALUE

FROM XDR\_PROJECT\_AIRWAYS ldarows

JOIN IP\_FLWSHT\_REC flwrec ON ldarows.INPATIENT\_DATA\_ID = flwrec.INPATIENT\_DATA\_ID

JOIN IP\_FLWSHT\_MEAS meas ON flwrec.FSD\_ID = meas.FSD\_ID

AND ldarows.LINE = meas.OCCURANCE

AND meas.MEAS\_VALUE IS NOT NULL

WHERE

( meas.RECORDED\_TIME >= ldarows.hosp\_admsn\_time )

AND ( ldarows.hosp\_dischrg\_time IS NULL OR meas.RECORDED\_TIME <= ldarows.hosp\_dischrg\_time )

AND ( ldarows.PLACEMENT\_INSTANT IS NULL OR meas.RECORDED\_TIME >= ldarows.PLACEMENT\_INSTANT )

AND ( ldarows.REMOVAL\_DTTM IS NULL OR meas.RECORDED\_TIME <= ldarows.REMOVAL\_DTTM );

/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

Calculate time for each Airway LDA

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/

create table xdr\_PROJECT\_ventminmax as

SELECT ip\_enc\_id, IP\_LDA\_ID,

MIN (hosp\_admsn\_time) as HospitalAdmission,

MAX (hosp\_dischrg\_time) as HospitalDischarge,

MIN (PLACEMENT\_INSTANT) as PlacementTime,

MAX (REMOVAL\_DTTM) as RemovalTime,

MIN (RECORDED\_TIME) as MinRecorded,

MAX (RECORDED\_TIME) as MaxRecorded

FROM xdr\_PROJECT\_air\_meas

GROUP BY ip\_enc\_id, IP\_LDA\_ID;

drop table xdr\_PROJECT\_venttime purge;

create table xdr\_PROJECT\_venttime as

select ip\_enc\_id,

round(sum(airwaydays),1) as total\_vent\_days

from (

SELECT ip\_enc\_id,

IP\_LDA\_ID,

MaxTimePerLDA - MinTimePerLDA as AirwayDays

FROM (

SELECT v.\*,

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

FROM xdr\_PROJECT\_ventminmax v

) x)

y

group by ip\_enc\_id;

select \* from XDR\_PROJECT\_VENTTIME;
```
## Health Maintenance Report

###### (by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 6/26/19)

This code allows to access historical data from the Health Maintenance Report. We just have to use the date given by the PI in reference to the extract\_date in the hm report, and use the last entry to find the corresponding the status and IDEAL\_RETURN\_DT each time.

Example: We must apply the selection for Colonoscopy screening:

Galaxy link: [https://datahandbook.epic.com/Reports/Details/1239812?rank=1&queryid=63476074&docid=87831](https://datahandbook.epic.com/Reports/Details/1239812?rank=1&queryid=63476074&docid=87831)
```sql
**Table**: **F\_HM\_TREND**, The fact table is a derived table that stores Health Maintenance information.  
This table is intended to take monthly snapshots to allow overtime trending on Health Maintenance topic data.  
Will allow for facility, department and provider level reports. This is an append-only table that does not make  
use of Start or End dates. [https://datahandbook.epic.com/ClarityDictionary/Details?tblName=F\_HM\_TREND](https://datahandbook.epic.com/ClarityDictionary/Details?tblName=F_HM_TREND)

##### **Health Maintenance Report**

\-- The cohort table includes a reference date to use when pulling the health maintenance at a particular point in time

select distinct coh.pat\_id

,coh.reference\_date

,hm.extract\_date

,hm.TOPIC\_NAME

,hm.IDEAL\_RETURN\_DT

,zst.name as due\_status\_dt1

,MAX(hm.extract\_date) OVER (PARTITION BY coh.pat\_id) AS latest\_measure

from xdr\_prinv\_coh coh

left JOIN F\_HM\_TREND hm ON coh.pat\_id = hm.pat\_id and trunc(hm.extract\_date) <= coh.date\_1

left join ZC\_HMT\_DUE\_STATUS zst ON hm.HMT\_DUE\_STATUS\_C = zst.HMT\_DUE\_STATUS\_C

where

hm.QUALIFIED\_HMT\_ID = 7770000132 --Colon Ca Screening: COLONOSCOPY

) x

WHERE x.extract\_date = latest\_measure

) dt1 on coh.pat\_id = dt1.pat\_id;

# Orders for Inpatient Transfers

###### (by [Robert Follett (Unlicensed)](https://uclabip.atlassian.net/wiki/people/557058:fb90de8e-f6e8-4a11-b471-2992e0b019af?ref=confluence) on 7/02/19)

This code will pull orders to admit a patient into the hospital.

##### **Inpatient Orders**

select op.pat\_id,

          op.pat\_enc\_csn\_id,

          admit\_ser.prov\_name as admit\_prov\_name,

          op.order\_time,

          op.display\_name,

          auth\_ser.prov\_name as auth\_prov\_name,

          op.proc\_id,

          op.ORD\_CREATR\_USER\_ID

from order\_proc op

join clarity\_eap eap on op.proc\_id = eap.proc\_id

join clarity\_ser admit\_ser on op.ORD\_CREATR\_USER\_ID = admit\_ser.user\_id

join clarity\_ser auth\_ser on op.authrzing\_prov\_id = auth\_ser.prov\_id

where op.proc\_id in (263, 327411, 327400,721947) -- Admit to IP

and op.order\_status\_c = 5

order by op.pat\_id, op.order\_time;
```
# BMT: Bone Marrow Transplant

###### (by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 7/10/19)

I have found some resources on how to find information for Bone Marrow Transplants. Since it's not one of the types in \[**TX\_CLASS\_C**\], the key is to use \[**SUM\_BLK\_TYPE\_ID**\] = 2050001100 "HSCT ALLOGENEIC RECIPIENT"

Inexplicably, this item is not in \[**ZC\_SUM\_BLK\_TYPE**\] but Ahson Shigri (the person that Star referred me to for transplant questions) told me about it.
```sql
##### **BMT counts**

select count(distinct pat.pat\_id)

from patient pat

join pat\_enc enc on pat.pat\_id = enc.pat\_id

join Episode\_Link lnk on enc.pat\_enc\_csn\_id = lnk.pat\_enc\_csn\_id

join Episode ep on lnk.episode\_id = ep.episode\_id

left join BMT\_INFO bmt on ep.episode\_id = bmt.SUMMARY\_BLOCK\_ID

where

lnk.SUM\_BLK\_TYPE\_ID = 2050001100 ----2050001100 "HSCT ALLOGENEIC RECIPIENT"

\--join TRANSPLANT\_INFO inf on ep.episode\_id = inf.SUMMARY\_BLOCK\_ID --nothing comes from this join
```
# MyChart Activity

###### (by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 7/17/19)

The final solution was to pull directly from

[**MYC\_PT\_USER\_ACCSS**](https://datahandbook.epic.com/ClarityDictionary/Details?tblName=MYC_PT_USER_ACCSS): This table stores an audit trail of actions taken within patient facing web applications. This includes MyChart, MyChart Bedside, and audits for web services intended for use by patient facing applications. MyChart Bedside events will populate WPR 535 instead of WPR 530 to indicate the type of audit event. The fields in this table track who took the action (typically the patient), what action was taken and when the action was taken.

*   **UA\_WHO\_ACCESSED:** If a patient accessed their own account, then this field stores that person's WPR ID (WPR .1). It may be the patient or another person who has proxy access to the patient's account.
*   **MYPT\_ID:** The unique ID of the WPR user record that was accessed. The WPR user could be a user of MyChart or a MyChart Bedside user.
*   **This means that if a mother(proxy) accessed her child’s account. The mom’s mypt\_id was recorded in this field, while the child’s was stored under mypt\_id. By comparing these two, we can know if the access was the patient herself, or a proxy.**

Additionally, the [**MYC\_PATIENT**](https://datahandbook.epic.com/ClarityDictionary/Details?tblName=MYC_PATIENT) includes a field called **PROXY\_ACCOUNT\_YN** that flags if the patient has a proxy account authorized to access his/her info.

The table [**PAT\_MYC\_PRXY\_HX**](https://datahandbook.epic.com/ClarityDictionary/Details?tblName=PAT_MYC_PRXY_HX) contains a historical record of all the proxies for any given patient. The field **PROXY\_PAT\_ID**: The unique ID of the patient who has proxy access to the record of the patient who is identified in **PAT\_ID**
```sql
**Field** 

##### **MyChart Activity**

SELECT distinct coh.pat\_id

,MYC.MYPT\_ID

,myc.PROXY\_ACCOUNT\_YN

,PRX.PROXY\_PAT\_ID

,mycPRX.MYPT\_ID AS PROXY\_MYPT\_ID

,act.UA\_WHO\_ACCESSED

,CASE WHEN MYC.MYPT\_ID = act.UA\_WHO\_ACCESSED THEN 0 ELSE 1 END PROXY\_ACCESS\_YN

,act.UA\_SESSION\_NUM

,act.myc\_ua\_type\_c

,act.MYC\_UA\_ACTION\_C

,act.BEDSIDE\_UA\_TYPE\_C

,ztyp.name as type

,zact.name as action

,act.UA\_PERSON\_TYPE\_C

,act.ua\_time

,act.UA\_EXTENDED\_INFO

FROM xdr\_prinv\_coh COH

LEFT JOIN PAT\_MYC\_PRXY\_HX PRX ON COH.PAT\_ID = PRX.PAT\_ID

JOIN MYC\_PATIENT myc ON coh.pat\_id = myc.pat\_id

LEFT JOIN MYC\_PATIENT mycPRX ON PRX.PROXY\_PAT\_ID = mycPRX.pat\_id

JOIN MYC\_PT\_USER\_ACCSS ACT ON MYC.MYPT\_ID = ACT.MYPT\_ID

AND act.MYC\_UA\_TYPE\_C IS NOT NULL

LEFT JOIN ZC\_MYC\_UA\_TYPE ztyp ON act.myc\_ua\_type\_c = ztyp.myc\_ua\_type\_c

LEFT JOIN ZC\_MYC\_UA\_ACTION zact ON act.MYC\_UA\_ACTION\_C = zact.MYC\_UA\_ACTION\_C

LEFT JOIN ZC\_BEDSIDE\_UA\_TYPE zbed ON act.BEDSIDE\_UA\_TYPE\_C = zbed.BEDSIDE\_UA\_TYPE\_C

WHERE

\-- Date range of interest for patient portal activity is 8/1/17 - 7/31/18.

TRUNC(act.ua\_time) BETWEEN '08/01/2015' AND '07/31/2018'

AND act.UA\_PERSON\_TYPE\_C = 1--patient

;

If you need to lookup the session information, you should resort to [**V\_MYC\_SESSIONS:**](https://datahandbook.epic.com/ClarityDictionary/Details?tblName=V_MYC_SESSIONS) This view should be used as the base table for reporting content that reports on the MyChart Session because it handles required filtering on the derived table underlying the view query. This view can be joined to MYC\_PT\_USER\_ACCSS for detailed information about what the user did during their MyChart session by using queries of the form outlined below.

*   *   **PROXY\_ACCESS\_BOOL**: This column is equal to 1 if the user who logged into MyChart accessed information about another MyChart user(proxy subject) during the session. Join this table to MYC\_PT\_USER\_ACCSS as described in the table description for detailed information on the proxy  
        subject(s) accessed during the session. _(After doing some work on the proxy access, I encountered this field not to be completely accurate and you should resort to the previous query to draw conclusions in this regard)_
    *   **MYPT\_ID**: If a patient accessed their own account, then this field stores that person's WPR ID (WPR .1). It may be the patient or another person who has proxy access to the patient's account.
    *   **PAT\_ID**: If the user who logged into MyChart is a patient, then this column contains a PAT\_ID, commonly used to join to the PATIENT table. If the user who logged into MyChart is a non-patient user, then this column is NULL. Because of this, INNER JOIN may not always be the appropriate join type for linking to the PATIENT table.

##### **MyChart Session Information (from EPIC docu)**

SELECT \* FROM V\_MYC\_SESSIONS INNER JOIN MYC\_PT\_USER\_ACCSS ON MYC\_PT\_USER\_ACCSS.UA\_WHO\_ACCESSED = F\_MYC\_SESSIONS.MYPT\_ID AND MYC\_PT\_USER\_ACCSS.UA\_SESSION\_NUM = F\_MYC\_SESSIONS.UA\_SESSION\_NUM

The query belows check the latest MyChart status of a patient.

##### **MyChart Patient Status**

select distinct pat\_id

,end\_dttm

,mychart\_status\_c

from f\_pat\_mychart\_status\_hx

where end\_dttm is null --this indicates the latest MyChart status of a patient

and mychart\_status\_c = 1 --this indicates that the latest MyChart status of a patient is "active"

;

/\*

PREVIOUS LOGIC (replaced by query above)

If you need to check if a patient is active in MyChart:

query i2b2.lz\_clarity\_pat\_contacts and check if active\_mychart is "Yes"

or check clarity.patient\_myc directly for mychart\_status\_c

or check clarity.myc\_patient directly for status\_cat\_c

select distinct coh.pat\_id

,active\_mychart

,pmc.mychart\_status\_c

,zpmc.name patient\_myc\_status

,mcp.status\_cat\_c

,zmcp.name myc\_patient\_status

from xdr\_prinv\_coh coh

left join i2b2.lz\_clarity\_pat\_contacts con on coh.pat\_id = con.pat\_id

left join clarity.patient\_myc pmc on coh.pat\_id = pmc.pat\_id

left join clarity.zc\_alt\_webste\_stat zpmc on pmc.mychart\_status\_c = zpmc.alt\_webste\_stat\_c

left join clarity.myc\_patient mcp on coh.pat\_id = mcp.pat\_id

left join clarity.zc\_myc\_status zmcp on mcp.status\_cat\_c = zmcp.myc\_status\_c

\-- the criteria below selects only active MyChart patients

\-- where nvl(con.active\_mychart,'No') = 'Yes' --default null to inactive

\-- or nvl(pmc.mychart\_status\_c,2) = 1 --default null to inactive

\-- or nvl(mcp.status\_cat\_c,2) = 1 --default null to inactive

;

\*/
```
## DEATH TABLE

[Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence)

We decided to create a death table to capture cases where patients, that do not have a "deceased" status in Care Connect, that have been found to be deceased through other venues. For instance, a PI team contacting patients for enrollment is told by a relative or their PCP that the patient is dead. Additionally, we are planning on adding those patients in OHIA's death registry once we determine the final validation process, aimed at finding which ones are very likely to be deceased, even though there is no final confirmation in Care Connect yet.
```sql
##### **DEATH TABLE CREATION**

DROP TABLE I2B2.LZ\_DEATH PURGE;

CREATE TABLE LZ\_DEATH

("PAT\_ID" VARCHAR2(10 BYTE)

,"SOURCE" VARCHAR2(256 BYTE) --Example: 'ACP - Project, IRB 18-001612'

,"USER" VARCHAR2(15 BYTE) --Example: 'JSANZ'

,CREATE\_DATE TIMESTAMP --use SYSDATE

,"COMMENT" VARCHAR2(1256 BYTE) --Example: 'The patients husband reported her to be deceased when receiving a notification from the research team on early September 2019. The PI team let us know'

,"DEATH\_DATE" DATE); --LEave it blank if we don't have it

We shall incorporate this table to the death function so that it's taken into account when producing the most up to date vital status for each patient. 

[Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) (can you implement the function part?) The table has been created already. Thanks

Hi, [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence), are we planning to have a death date in this table?  Thanks!

# Face to Face Visits

###### (by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 9/5/19)

([Robert Follett (Unlicensed)](https://uclabip.atlassian.net/wiki/people/557058:fb90de8e-f6e8-4a11-b471-2992e0b019af?ref=confluence) - Added telemedicine and telephone because of Covid)

The following identifies face-to-face visits.

1.  visit\_type = 'AV'
    1.  encounter\_date <= sysdate
    2.  nvl(appointment\_status,'Completed') = 'Completed'
2.  visit\_type != 'AV'
    1.  encounter\_date <= sysdate
    2.  appointment\_status = 'Completed'
    3.  encounter types listed in face-to-face encounter types sheet (I:\\\_BIP\\xDR\\Lists and Codes\\Face to Face Visits.xlsx)

##### **Face to Face Visits Query**

FROM i2b2.lz\_clarity\_enc e

JOIN i2b2.lz\_enc\_visit\_types vt ON e.pat\_enc\_csn\_id = vt.pat\_enc\_csn\_id

WHERE e.effective\_date\_dt < SYSDATE --Completed appts are prior to current date

AND ((vt.visit\_type = 'AV' --Ambulatory Visit

AND (e.appt\_status\_c IS NULL OR e.appt\_status\_c = '2') --Completed (includes null)

)

OR

(vt.visit\_type <> 'AV' --Non-Ambulatory Visit

AND e.appt\_status\_c = '2' --Completed (does not include null)

AND e.enc\_type\_c IN ('1000' --Initial consult --Face-to-face encounter types per I:\\\_BIP\\xDR\\Lists and Codes\\Face to Face Visits.xlsx

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

\--,'70' --Telephone (Remove per DB on 3/14/2022)

,'76' --Telemedicine

\--,'21076' --Tele-Medicine (Remove per DB on 3/14/2022)

)

))

# Chief Complaints

PAT\_ENC\_RSN\_VISIT. ENC\_REASON\_NAME obtains the chief complaints for the cohort.

Modified on 06/07/2021 by Rob Follett - Added the CL\_RSN\_FOR\_VISIT lookup table.

left join (select distinct lk.pat\_mrn\_id as mrn

,vs.pat\_enc\_csn\_id

,listagg(rfv.display\_text, '||') within group (order by vs.line) as chief\_complaint\_list

from xdr\_nnnnn\_cohort lk

join clarity.pat\_enc\_rsn\_visit vs on lk.pat\_id = vs.pat\_id

join CL\_RSN\_FOR\_VISIT rfv on vs.enc\_reason\_id = rfv.reason\_visit\_id

group by lk.pat\_mrn\_id,vs.pat\_enc\_csn\_id

) chief on cohort.pat\_enc\_csn\_id = chief.pat\_enc\_csn\_id

\--TT-9/26/2023: Alternate query

left join (select pat\_enc\_csn\_id, listagg(trim(enc\_reason\_name), '|') within group (order by pat\_enc\_csn\_id, line) as chief\_complaint\_list

from (select distinct vs.pat\_id, vs.pat\_enc\_csn\_id, vs.line, vs.enc\_reason\_name

from p\_coh lk

join clarity.pat\_enc\_rsn\_visit vs on lk.pat\_id = vs.pat\_id)

group by pat\_enc\_csn\_id

) chief on coh.pat\_enc\_csn\_id = chief.pat\_enc\_csn\_id

# Current Med List

[Robert Follett (Unlicensed)](https://uclabip.atlassian.net/wiki/people/557058:fb90de8e-f6e8-4a11-b471-2992e0b019af?ref=confluence)

PAT\_ENC\_CURR\_MEDS table enables you to report on current (as well as active) medications per encounter as listed in clinical system.

##### **Current Med List**

\--Pull the latest entries in PAT\_ENC\_CURR\_MEDS, then reject if inactive or historical.

\-- If active flag is null, treat as active.

select x.\*

,m.\*

from (select distinct cm.pat\_id

,cm.current\_med\_id

,cm.contact\_date

,cm.is\_active\_yn

,max(cm.contact\_date) over (partition by cm.pat\_id) latest\_contact\_date

from pat\_enc\_curr\_meds cm

where nvl(cm.is\_active\_yn,'Y') = 'Y'

) x

join i2b2.lz\_clarity\_meds m on x.current\_med\_id = m.order\_med\_id

where x.contact\_date = x.latest\_contact\_date

and m.order\_class <> 'Historical Med'

order by x.pat\_id, x.current\_med\_id

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

/\* trying to convert multiple rows/columns into columns per patient using pivot and unpivot

phone numbers have multiple values and have to incorporate contact priority

first using unpivot - making all phone numbers and contact priority into multiple rows and

then using pivot - converting values with multiple rows into multiple columns per patient

\*/

with home\_result as

(

Select coh.pat\_ID,

othcom.name as phone\_type,

cm1.OTHER\_COMMUNIC\_NUM as phone ,

cm1.line,

case when cm1.CONTACT\_PRIORITY is not null then cm1.CONTACT\_PRIORITY else 99 end as CONTACT\_PRIORITY,

concat(SUBSTR( othcom.name, 1 , 1 ), (case when cm1.CONTACT\_PRIORITY is not null then cm1.CONTACT\_PRIORITY else 0 end )) as Contactpriorityorder

from XDR\_PRINV\_COH coh

inner join OTHER\_COMMUNCTN cm1 on coh.pat\_id = cm1.pat\_id --and cm1.OTHER\_COMMUNIC\_C = 7 --Home Phone

left outer join ZC\_OTHER\_COMMUNIC othcom on othcom.OTHER\_COMMUNIC\_C = cm1.OTHER\_COMMUNIC\_C

/\* if multiple values, grouping them at patient level \*/

WHERE

\-- Exclude obvious place holders or invalid phone #s

cm1.OTHER\_COMMUNIC\_NUM is not null

and cm1.OTHER\_COMMUNIC\_NUM not in ('000-000-0000'

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

PAT\_ID

,"'PHONE\_1'" as "phone1"

,"'CONTACTPRIORITYORDER\_2'" AS "contactpriority\_phone1"

,"'PHONE\_3'" AS "phone2"

,"'CONTACTPRIORITYORDER\_4'" AS "contactpriority\_phone2"

,"'PHONE\_5'" AS "phone3"

,"'CONTACTPRIORITYORDER\_6'" AS "contactpriority\_phone3"

,"'PHONE\_7'" AS "phone4"

,"'CONTACTPRIORITYORDER\_8'" AS "contactpriority\_phone4"

,CONCAT(

CONCAT(

CONCAT(CASE WHEN "'CONTACTPRIORITYORDER\_2'" IS NOT NULL THEN "'CONTACTPRIORITYORDER\_2'" ELSE 'NA' END, '| ')

,CONCAT(CASE WHEN "'CONTACTPRIORITYORDER\_4'" IS NOT NULL THEN "'CONTACTPRIORITYORDER\_4'" ELSE 'NA' END, '| ' )

)

,CONCAT(

CONCAT(CASE WHEN "'CONTACTPRIORITYORDER\_6'" IS NOT NULL THEN "'CONTACTPRIORITYORDER\_6'" ELSE 'NA' END, '| ' )

,CASE WHEN "'CONTACTPRIORITYORDER\_8'" IS NOT NULL THEN "'CONTACTPRIORITYORDER\_8'" ELSE 'NA' END)

)

AS "contactpriorityorder\_phone1234"

FROM (

SELECT PAT\_ID

,concat(

concat( col , '\_'), ROW\_NUMBER() OVER (PARTITION BY PAT\_ID ORDER BY orderpriority ASC)

) AS col

,value

FROM (

SELECT PAT\_ID

,cast ( phone as varchar2(30)) as phone

,cast(Contactpriorityorder as varchar2(30)) as Contactpriorityorder

, ROW\_NUMBER() OVER (PARTITION BY PAT\_ID ORDER BY CONTACT\_PRIORITY ASC) as orderpriority

FROM home\_result

GROUP BY PAT\_ID, phone, Contactpriorityorder, CONTACT\_PRIORITY

) rt

unpivot ( value FOR col in ( phone, Contactpriorityorder))unpiv

) tp

pivot ( Max(value) FOR col in ('PHONE\_1'

,'CONTACTPRIORITYORDER\_2','PHONE\_3','CONTACTPRIORITYORDER\_4',

'PHONE\_5','CONTACTPRIORITYORDER\_6','PHONE\_7','CONTACTPRIORITYORDER\_8'

)

) piv

order by 1;
```
## Beaker Text Reports

###### (BY [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) ON 9/26/19)

The following code pulls the text based results (comments, narratives, etc...) using the case\_id as a reference. A common use case for this resource is when the usual query returns "This result contains rich text formatting which cannot be displayed here" as a result. The following script is the original code sent to us. Below it, you can find a more specific application of it.

This script can pull results after January 2016 (Beaker) for previous results this might not work
```sql
##### **Original Query**

WITH

CaseIDsToProcess AS (

SELECT LAB\_CASE\_DB\_MAIN.CASE\_ID

FROM LAB\_CASE\_DB\_MAIN

WHERE CASE\_ID IN (818130 )

),ExternalPatientInfo AS (

SELECT REQ\_GROUPER\_ID, REQ\_SUBMITTER\_ID, REQ\_SUBM\_PT\_ID, REQ\_INTERVAL\_NUMBER, REQ\_ACCOUNT\_ID, REQ\_CHART\_NUM, EXTERNAL\_VISIT\_ID FROM (

SELECT DISTINCT REQ\_GROUPER\_ID, REQ\_SUBMITTER\_ID, REQ\_SUBM\_PT\_ID, REQ\_INTERVAL\_NUMBER, REQ\_ACCOUNT\_ID, REQ\_CHART\_NUM, EXTERNAL\_VISIT\_ID,

RANK() OVER (PARTITION BY REQ\_GROUPER\_ID, REQ\_SUBMITTER\_ID, REQ\_INTERVAL\_NUMBER, REQ\_ACCOUNT\_ID, REQ\_CHART\_NUM ORDER BY REquisition\_ID DESC, NVL(EXTERNAL\_VISIT\_ID,'-') DESC, REQ\_SUBM\_PT\_ID) as Rankorder

FROM CLARITY.REQ\_DB\_MAIN

WHERE

REQ\_DB\_MAIN.REQUISITION\_ID IN (SELECT CASE\_ID FROM CaseIDsToProcess )

AND REQ\_SUBMITTER\_ID IS NOT NULL) REQDBMAINResults

WHERE REQDBMAINResults.RankOrder=1

),

PatientInformation AS (

SELECT

DISTINCT

CASE WHEN NVL(REQ\_DB\_MAIN.REQ\_GROUPER\_ID,0) <>0 THEN CAST (NVL(REQ\_DB\_MAIN.REQ\_GROUPER\_ID,'0') AS VARCHAR2(25)) ELSE PATIENT.PAT\_ID END || '-' ||LAB\_CASE\_DB\_MAIN.CASE\_ID uniqueid,

LAB\_CASE\_DB\_MAIN.CASE\_ID

FROM CaseIDsToProcess

INNER JOIN CLARITY.LAB\_CASE\_DB\_MAIN ON CaseIDsToProcess.CASE\_ID=LAB\_CASE\_DB\_MAIN.CASE\_ID

LEFT JOIN CLARITY.PATIENT ON PATIENT.PAT\_ID=LAB\_CASE\_DB\_MAIN.CASE\_PAT\_ID

LEFT JOIN CLARITY.REQ\_CASES ON REQ\_CASES.CASES\_ID = CaseIDsToProcess.CASE\_ID

LEFT JOIN CLARITY.REQ\_DB\_MAIN ON REQ\_DB\_MAIN.REQUISITION\_ID = CLARITY.REQ\_CASES.REQUISITION\_ID

LEFT JOIN CLARITY.RQG\_DB\_MAIN ON REQ\_DB\_MAIN.REQ\_GROUPER\_ID = RQG\_DB\_MAIN.RQG\_GROUPER\_ID

LEFT JOIN CLARITY.SPEC\_DB\_MAIN ON LAB\_CASE\_DB\_MAIN.CASE\_ID=SPEC\_DB\_MAIN.CASE\_ID

LEFT JOIN CLARITY.LAB\_SMT\_NOADD ON SPEC\_DB\_MAIN.REQ\_SMT\_ID = Lab\_Smt\_Noadd.Record\_Id

LEFT JOIN CLARITY.REQ\_ORDER\_GROUP ON REQ\_ORDER\_GROUP.REQUISITION\_ID=LAB\_CASE\_DB\_MAIN.CASE\_ID

LEFT JOIN CLARITY.ORDER\_PROC ON REQ\_ORDER\_GROUP.ORDER\_ID=ORDER\_PROC.ORDER\_PROC\_ID

LEFT JOIN ExternalPatientInfo ON REQ\_DB\_MAIN.REQ\_GROUPER\_ID = ExternalPatientInfo.REQ\_GROUPER\_ID

),

PatientCases AS

(

SELECT

DISTINCT

PatientInformation.uniqueid,

LAB\_CASE\_INFO.CASE\_NUM,

LAB\_CASE\_DB\_MAIN.CASE\_ID,

LAB\_CASE\_INFO.AP\_CASE\_STATUS\_C

FROM CaseIDsToProcess

INNER JOIN CLARITY.LAB\_CASE\_DB\_MAIN ON CaseIDsToProcess.CASE\_ID=LAB\_CASE\_DB\_MAIN.CASE\_ID

INNER JOIN CLARITY.LAB\_CASE\_INFO ON LAB\_CASE\_INFO.REQUISITION\_ID = LAB\_CASE\_DB\_MAIN.CASE\_ID

INNER JOIN CLARITY.ZC\_AP\_CASE\_STATUS ON ZC\_AP\_CASE\_STATUS.AP\_CASE\_STATUS\_C = LAB\_CASE\_INFO.AP\_CASE\_STATUS\_C

INNER JOIN PatientInformation ON CaseIDsToProcess.CASE\_ID=PatientInformation.Case\_Id

),

CASERESULT AS (

SELECT

UNIQUEID,

CASE\_ID,

CASE\_NUM,

RESULT\_ID,

AP\_CASE\_STATUS\_C,

RES\_VAL\_STATUS\_C,

Line,

Value\_Line,

COMPONENT\_ID,

EXTERNAL\_NAME,

REPLACE(REPLACE(MULT\_LN\_VAL\_STORAGE,'\\','\\\\'),'"','\\"') AS ResultText,

CMP\_MULTILINE\_VALUE

FROM

(

SELECT DISTINCT

h.uniqueid,

a.Case\_ID,

h.CASE\_NUM,

c.RESULT\_ID,

d.COMPONENT\_ID,

NVL(e.CMP\_MULTILINE\_VALUE,1) AS CMP\_MULTILINE\_VALUE,

NVL(d.LINE,1) AS LINE,

NVL(f.VALUE\_LINE,1) AS VALUE\_LINE ,

g.EXTERNAL\_NAME,

NVL(f.MULT\_LN\_VAL\_STORAGE,NVL(d.COMPONENT\_RESULT,'')) AS "MULT\_LN\_VAL\_STORAGE",

RANK() OVER ( Partition BY a.CASE\_ID Order by c.Result\_ID DESC) AS "RANK",

c.RES\_VAL\_STATUS\_C,

h.AP\_CASE\_STATUS\_C

FROM CaseIDsToProcess a

, CLARITY.SPEC\_DB\_MAIN b

, CLARITY.RES\_DB\_MAIN c

, CLARITY.Res\_Components d

, CLARITY.RES\_VAL\_PTR\_RM e

, CLARITY.RES\_VAL\_DATA\_RM f

, CLARITY.CLARITY\_COMPONENT g

, PatientCases h

WHERE

a.CASE\_ID=b.CASE\_ID

AND a.CASE\_ID=h.CASE\_ID

AND b.SPECIMEN\_ID = c.RES\_SPECIMEN\_ID

AND c.RESULT\_ID = d.RESULT\_ID

AND d.LINE = e.GROUP\_LINE (+)

AND d.RESULT\_ID = e.RESULT\_ID (+)

AND e.CMP\_MULTILINE\_VALUE = f.GROUP\_LINE (+)

AND e.RESULT\_ID = f.RESULT\_ID (+)

AND d.COMPONENT\_ID= g.COMPONENT\_ID

\-- AND c.RES\_VAL\_STATUS\_C = 9

AND d.COMPONENT\_REPORT\_YN=1

AND d.COMPONENT\_ID <> 7100796

) RESULT

WHERE RANK=1

AND MULT\_LN\_VAL\_STORAGE IS NOT NULL

)SELECT UNIQUEID,CASE\_ID,CASE\_NUM, RESULT\_ID,AP\_CASE\_STATUS\_C,RES\_VAL\_STATUS\_C,Line,Value\_Line,COMPONENT\_ID,EXTERNAL\_NAME,ResultText,CMP\_MULTILINE\_VALUE FROM CaseResult ORDER BY UNIQUEID, LINE, CMP\_MULTILINE\_VALUE, VALUE\_LINE;

##### **Pull Beaker Results - Use Case**

/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

Pull the orders for your cohort

\---------------------------------------------------

Use any selections that might apply to your particular case

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/

DROP TABLE xdr\_prinv\_opr PURGE;

CREATE TABLE xdr\_prinv\_opr AS

SELECT DISTINCT pat.pat\_id

,pat.pat\_mrn\_id

,pat.study\_id

,opr.order\_proc\_id

,opr.proc\_id

,opr.description

,eap.proc\_name

,opr.proc\_code

,opr.order\_time

,opr.result\_time

,opr.order\_type\_c

,xot.NAME AS order\_type

,opr.abnormal\_yn

,opr.order\_status\_c

,opr.radiology\_status\_c

,opr.specimen\_source\_c

,opr.specimen\_type\_c

,acc.acc\_num

,xpt.NAME AS specimen\_type

,res.line

,res.ord\_value

,res.component\_id

,cmp.NAME AS component\_name

,res.component\_comment

,res.result\_time AS res\_result\_time

,res.result\_val\_start\_ln

,res.result\_val\_end\_ln

,res.result\_cmt\_start\_ln

,res.result\_cmt\_end\_ln

,res.lrr\_based\_organ\_id

FROM xdr\_prinv\_enc pat

JOIN order\_proc opr ON pat.pat\_enc\_csn\_id = opr.pat\_enc\_csn\_id

LEFT JOIN order\_results res ON opr.order\_proc\_id = res.order\_proc\_id

LEFT JOIN order\_rad\_acc\_num acc ON opr.order\_proc\_id = acc.order\_proc\_id

LEFT JOIN clarity\_eap eap ON opr.proc\_id = eap.proc\_id

LEFT JOIN clarity\_component cmp ON res.component\_id = cmp.component\_id

LEFT JOIN zc\_order\_type xot ON opr.order\_type\_c = xot.order\_type\_c

LEFT JOIN zc\_specimen\_type xpt ON opr.specimen\_type\_c = xpt.specimen\_type\_c

WHERE opr.order\_status\_c = 5

\-- for referrals, see comments above for order\_status\_c IN (2,4)

\--AND opr.order\_type\_c NOT IN (7,26,62,63) --Not labs --not really necessary when a selection is made in the following lines

\--AND (opr.order\_type\_c IN (5,1003) OR opr.radiology\_status\_c = 99) --Imaging

\--AND (opr.order\_type\_c = 3) --Microbiology

\--AND (opr.order\_type\_c = 59) --Pathology

\--AND (opr.order\_type\_c = 8) --Outpatient Referral

;

/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

After you investigate the specific procedures/components

that apply to your case, pull the case\_id's related

to them

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/

DROP TABLE xdr\_prinv\_opr\_selection PURGE;

CREATE TABLE xdr\_prinv\_selection AS

select DISTINCT

\-- requisition\_id matches the case\_id

cas.requisition\_id

,opr.\*

from XDR\_ABDALLA\_OPR\_SAMPLE opr

JOIN LAB\_CASE\_INFO cas ON opr.acc\_num = cas.CASE\_NUM -- this is added join that allows to find the case\_idwhere

opr.proc\_id = '77929'

and opr.component\_id = '7000004'

;

/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

Investigate the specific procedures/components

that apply to your case

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/

drop table xdr\_prinv\_opr\_results purge;

create table xdr\_prinv\_opr\_results as

WITH

CaseIDsToProcess AS (

SELECT requisition\_id as CASE\_ID

FROM xdr\_prinv\_selection

)

),ExternalPatientInfo AS (

SELECT REQ\_GROUPER\_ID, REQ\_SUBMITTER\_ID, REQ\_SUBM\_PT\_ID, REQ\_INTERVAL\_NUMBER, REQ\_ACCOUNT\_ID, REQ\_CHART\_NUM, EXTERNAL\_VISIT\_ID FROM (

SELECT DISTINCT REQ\_GROUPER\_ID, REQ\_SUBMITTER\_ID, REQ\_SUBM\_PT\_ID, REQ\_INTERVAL\_NUMBER, REQ\_ACCOUNT\_ID, REQ\_CHART\_NUM, EXTERNAL\_VISIT\_ID,

RANK() OVER (PARTITION BY REQ\_GROUPER\_ID, REQ\_SUBMITTER\_ID, REQ\_INTERVAL\_NUMBER, REQ\_ACCOUNT\_ID, REQ\_CHART\_NUM ORDER BY REquisition\_ID DESC, NVL(EXTERNAL\_VISIT\_ID,'-') DESC, REQ\_SUBM\_PT\_ID) as Rankorder

FROM CLARITY.REQ\_DB\_MAIN

WHERE

REQ\_DB\_MAIN.REQUISITION\_ID IN (SELECT CASE\_ID FROM CaseIDsToProcess )

AND REQ\_SUBMITTER\_ID IS NOT NULL) REQDBMAINResults

WHERE REQDBMAINResults.RankOrder=1

),

PatientInformation AS (

SELECT

DISTINCT

CASE WHEN NVL(REQ\_DB\_MAIN.REQ\_GROUPER\_ID,0) <>0 THEN CAST (NVL(REQ\_DB\_MAIN.REQ\_GROUPER\_ID,'0') AS VARCHAR2(25)) ELSE PATIENT.PAT\_ID END || '-' ||LAB\_CASE\_DB\_MAIN.CASE\_ID uniqueid,

LAB\_CASE\_DB\_MAIN.CASE\_ID

FROM CaseIDsToProcess

INNER JOIN CLARITY.LAB\_CASE\_DB\_MAIN ON CaseIDsToProcess.CASE\_ID=LAB\_CASE\_DB\_MAIN.CASE\_ID

LEFT JOIN CLARITY.PATIENT ON PATIENT.PAT\_ID=LAB\_CASE\_DB\_MAIN.CASE\_PAT\_ID

LEFT JOIN CLARITY.REQ\_CASES ON REQ\_CASES.CASES\_ID = CaseIDsToProcess.CASE\_ID

LEFT JOIN CLARITY.REQ\_DB\_MAIN ON REQ\_DB\_MAIN.REQUISITION\_ID = CLARITY.REQ\_CASES.REQUISITION\_ID

LEFT JOIN CLARITY.RQG\_DB\_MAIN ON REQ\_DB\_MAIN.REQ\_GROUPER\_ID = RQG\_DB\_MAIN.RQG\_GROUPER\_ID

LEFT JOIN CLARITY.SPEC\_DB\_MAIN ON LAB\_CASE\_DB\_MAIN.CASE\_ID=SPEC\_DB\_MAIN.CASE\_ID

LEFT JOIN CLARITY.LAB\_SMT\_NOADD ON SPEC\_DB\_MAIN.REQ\_SMT\_ID = Lab\_Smt\_Noadd.Record\_Id

LEFT JOIN CLARITY.REQ\_ORDER\_GROUP ON REQ\_ORDER\_GROUP.REQUISITION\_ID=LAB\_CASE\_DB\_MAIN.CASE\_ID

LEFT JOIN CLARITY.ORDER\_PROC ON REQ\_ORDER\_GROUP.ORDER\_ID=ORDER\_PROC.ORDER\_PROC\_ID

LEFT JOIN ExternalPatientInfo ON REQ\_DB\_MAIN.REQ\_GROUPER\_ID = ExternalPatientInfo.REQ\_GROUPER\_ID

),

PatientCases AS

(

SELECT

DISTINCT

PatientInformation.uniqueid,

LAB\_CASE\_INFO.CASE\_NUM,

LAB\_CASE\_DB\_MAIN.CASE\_ID,

LAB\_CASE\_INFO.AP\_CASE\_STATUS\_C

FROM CaseIDsToProcess

INNER JOIN CLARITY.LAB\_CASE\_DB\_MAIN ON CaseIDsToProcess.CASE\_ID=LAB\_CASE\_DB\_MAIN.CASE\_ID

INNER JOIN CLARITY.LAB\_CASE\_INFO ON LAB\_CASE\_INFO.REQUISITION\_ID = LAB\_CASE\_DB\_MAIN.CASE\_ID

INNER JOIN CLARITY.ZC\_AP\_CASE\_STATUS ON ZC\_AP\_CASE\_STATUS.AP\_CASE\_STATUS\_C = LAB\_CASE\_INFO.AP\_CASE\_STATUS\_C

INNER JOIN PatientInformation ON CaseIDsToProcess.CASE\_ID=PatientInformation.Case\_Id

),

CASERESULT AS (

SELECT

UNIQUEID,

CASE\_ID,

CASE\_NUM,

RESULT\_ID,

AP\_CASE\_STATUS\_C,

RES\_VAL\_STATUS\_C,

Line,

Value\_Line,

COMPONENT\_ID,

EXTERNAL\_NAME,

REPLACE(REPLACE(MULT\_LN\_VAL\_STORAGE,'\\','\\\\'),'"','\\"') AS ResultText,

CMP\_MULTILINE\_VALUE

FROM

(

SELECT DISTINCT

h.uniqueid,

a.Case\_ID,

h.CASE\_NUM,

c.RESULT\_ID,

d.COMPONENT\_ID,

NVL(e.CMP\_MULTILINE\_VALUE,1) AS CMP\_MULTILINE\_VALUE,

NVL(d.LINE,1) AS LINE,

NVL(f.VALUE\_LINE,1) AS VALUE\_LINE ,

g.EXTERNAL\_NAME,

NVL(f.MULT\_LN\_VAL\_STORAGE,NVL(d.COMPONENT\_RESULT,'')) AS "MULT\_LN\_VAL\_STORAGE",

RANK() OVER ( Partition BY a.CASE\_ID Order by c.Result\_ID DESC) AS "RANK",

c.RES\_VAL\_STATUS\_C,

h.AP\_CASE\_STATUS\_C

FROM CaseIDsToProcess a

, CLARITY.SPEC\_DB\_MAIN b

, CLARITY.RES\_DB\_MAIN c

, CLARITY.Res\_Components d

, CLARITY.RES\_VAL\_PTR\_RM e

, CLARITY.RES\_VAL\_DATA\_RM f

, CLARITY.CLARITY\_COMPONENT g

, PatientCases h

WHERE

a.CASE\_ID=b.CASE\_ID

AND a.CASE\_ID=h.CASE\_ID

AND b.SPECIMEN\_ID = c.RES\_SPECIMEN\_ID

AND c.RESULT\_ID = d.RESULT\_ID

AND d.LINE = e.GROUP\_LINE (+)

AND d.RESULT\_ID = e.RESULT\_ID (+)

AND e.CMP\_MULTILINE\_VALUE = f.GROUP\_LINE (+)

AND e.RESULT\_ID = f.RESULT\_ID (+)

AND d.COMPONENT\_ID= g.COMPONENT\_ID

\-- AND c.RES\_VAL\_STATUS\_C = 9

AND d.COMPONENT\_REPORT\_YN=1

AND d.COMPONENT\_ID <> 7100796

) RESULT

WHERE RANK=1

AND MULT\_LN\_VAL\_STORAGE IS NOT NULL

)SELECT UNIQUEID,CASE\_ID,CASE\_NUM, RESULT\_ID,AP\_CASE\_STATUS\_C,RES\_VAL\_STATUS\_C,Line,Value\_Line,COMPONENT\_ID,EXTERNAL\_NAME,ResultText,CMP\_MULTILINE\_VALUE

FROM CaseResult

where component\_id = 7000004 --MICROSCOPIC DESCRIPTION

ORDER BY UNIQUEID, LINE, CMP\_MULTILINE\_VALUE, VALUE\_LINE

;

/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

If you need to merge the results into one line,

you may use the following pice

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/

DROP TABLE xdr\_prinv\_opr\_results\_merge PURGE;

CREATE TABLE xdr\_prinv\_opr\_results\_merge AS

SELECT uniqueid,case\_id,RTRIM(XMLAGG(XMLELEMENT(E,resulttext,'').EXTRACT('//text()')

ORDER BY value\_line).GetClobVal(),',') as result\_text\_merged

from xdr\_prinv\_opr\_results

GROUP BY uniqueid,case\_id;
```
## Check Patient's Contact Information

###### (by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 10/08/19)

The following code can be used to check if/when a patient has (or not) different forms of contact. The BIP\_PAT\_GEOCODE table contains patient's geocoded address.

Note: this looks for valid address AND phone AND email.   If you want to pull a patient that has at least one form of valid contact use OR instead of AND in the code below
```sql
SELECT \*

FROM xdr\_prinv\_coh coh

JOIN patient pat ON coh.pat\_id = pat.pat\_id

left join JSANZ.BIP\_PAT\_GEOCODE geo on pat.pat\_id = geo.pat\_id

where

\---------------------------------------------------------------------------------

\-- excludes patient's without a valid address, or labeled as homeless

\---------------------------------------------------------------------------------

geo.add\_line\_1is not null

and UPPER(geo.add\_line\_1) NOT like '%HOMELESS%'

and geo.fips is not null

AND -- OR

\---------------------------------------------------------------------------------

\-- excludes patients with a placeholder/dummy phone # or no phone# at all

\-- This can be extended to other phone numbers as well (work phone, cell, etc...)

\---------------------------------------------------------------------------------

( pat.home\_phone is not null

and pat.home\_phone NOT IN ('000-000-0000'

,'000-000-0001'

,'310-000-0000'

,'310-450-1748'

,'999-999-9999'

)

)

\---------------------------------------------------------------------------------

\-- excludes patients without email address

\---------------------------------------------------------------------------------

AND -- OR

pat.email\_address is not null

# All Flowsheet Questions on a Template

###### (BY [Robert Follett (Unlicensed)](https://uclabip.atlassian.net/wiki/people/557058:fb90de8e-f6e8-4a11-b471-2992e0b019af?ref=confluence) ON 10/10/19)

The following code pulls the different flowsheet questions on a given template id.  Use this to see what questions are asked on a template.

select

t.template\_id,

t.template\_name,

t.display\_name template\_display\_name,

tc.flo\_meas\_id group\_meas\_id,

fgd.disp\_name group\_meas\_name,

fgd.flo\_meas\_name group\_flo\_meas\_name,

fgd2.flo\_meas\_id flo\_meas\_id,

fgd2.flo\_meas\_name flo\_meas\_name,

fgd2.disp\_name flo\_disp\_name

from

ip\_flt\_data t

left join ip\_flt\_comps tc on (t.template\_id = tc.template\_id)

left join ip\_flo\_gp\_data fgd on (tc.flo\_meas\_id = fgd.flo\_meas\_id)

left join ip\_flo\_measuremnts fm on (fgd.flo\_meas\_id = fm.id)

join ip\_flo\_gp\_data fgd2 on (fm.measurement\_id = fgd2.flo\_meas\_id)

where t.template\_id = 281

group by

t.template\_id, t.template\_name, t.display\_name,

tc.line, tc.flo\_meas\_id, fgd.disp\_name, fgd.flo\_meas\_name,

fgd2.flo\_meas\_id, fgd2.flo\_meas\_name, fgd2.flo\_dis\_name, fgd2.flo\_row\_name, fgd2.flo\_row\_name,

fgd2.value\_type\_name, fgd2.description, fgd2.disp\_name

# Past Medical History

###### (BY [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) ON 12/02/19)

The following code pulls the Past Medical History information. This is a report that can be seen in CC, and the layout of the pull replicates this format. It uses the dx description for the diagnosis, as this is what it's shown in CC.

##### **Past Medical History**

select x.pat\_id

,x.study\_id

,x.icd\_code

,x.dx\_name as diagnosis\_desc

,x.medical\_hx\_date

,x.comments

from (select distinct coh.pat\_id

,coh.study\_id

,case when pl.contact\_date <= '01/01/2015' then cin9.code else cin10.code end icd\_code

,edg.dx\_name

,pl.contact\_date

,pl.medical\_hx\_date

,pl.comments

,pl.dx\_id

,MIN(pl.contact\_date) OVER (PARTITION BY pl.pat\_id, pl.dx\_id) AS first\_dx

From XDR\_PRINV\_COH coh

JOIN MEDICAL\_HX pl ON coh.pat\_id = pl.pat\_id

left join ZC\_HISTORY\_SOURCE zhs on pl.MED\_HX\_SOURCE\_C = zhs.HISTORY\_SOURCE\_C

\--This join helps us find the dx description that matches what can be seen in CC

LEFT JOIN clarity.CLARITY\_EDG edg ON pl.dx\_id = edg.dx\_id --AND cin9.line = 1

\-- ICD9 CODES JOIN

LEFT JOIN clarity.edg\_current\_icd9 cin9 ON pl.dx\_id = cin9.dx\_id AND cin9.line = 1

\--ICD10 CODES JOIN

LEFT JOIN clarity.edg\_current\_icd10 cin10 ON pl.DX\_ID = cin10.dx\_id AND cin10.line = 1

) x

where

first\_dx = x.contact\_date

order by pat\_id,diagnosis\_desc;
```
## Procedure Performing and Billing Providers

###### (by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 3/2/20)

The following code enhances the procedure data pull to add the performing and billing providers. It only applies to the CPT (not legacy but **hsp\_transactions**, and **arpb\_transactions**) and ICD-9/10 codes (**i2b2**.**lz\_clarity\_procedures**)
```sql
-- Procedure Performance Provider**

DROP TABLE xdr\_prinv\_prc PURGE;

CREATE TABLE xdr\_prinv\_prc AS

SELECT DISTINCT enc.pat\_id

,coh.study\_id

,hspt.pat\_enc\_csn\_id

,hspt.service\_date AS proc\_date

,SUBSTR(COALESCE(hspt.hcpcs\_code,hspt.cpt\_code),1,5) AS proc\_code

,'CPT-Hospital' AS proc\_type

,hspt.PERFORMING\_PROV\_ID

,hspt.BILLING\_PROV\_ID AS BILLING\_PROV\_ID

,EAP.PROC\_NAME

FROM i2b2.lz\_clarity\_enc enc

JOIN xdr\_prinv\_coh coh ON enc.pat\_id = coh.pat\_id

JOIN hsp\_transactions hspt ON enc.pat\_enc\_csn\_id = hspt.pat\_enc\_csn\_id

LEFT OUTER JOIN f\_arhb\_inactive\_tx fait ON hspt.tx\_id = fait.tx\_id

LEFT JOIN clarity\_eap eap ON SUBSTR(COALESCE(hspt.hcpcs\_code,hspt.cpt\_code),1,5) = eap.proc\_code

WHERE hspt.tx\_type\_ha\_c = 1

AND (LENGTH(hspt.cpt\_code) = 5 OR hspt.hcpcs\_code IS NOT NULL)

AND fait.tx\_id IS NULL;

CREATE INDEX xdr\_prinv\_prc\_patidx ON xdr\_prinv\_prc (pat\_id) nologging;

SELECT COUNT(\*),COUNT(DISTINCT pat\_id) FROM xdr\_prinv\_prc; --

SELECT COUNT(\*),COUNT(DISTINCT pat\_id) FROM xdr\_prinv\_prc WHERE PERFORMING\_PROV\_ID IS NOT NULL; --

ALTER TABLE XDR\_prinv\_PRC MODIFY proc\_type varchar2 (200);

INSERT INTO XDR\_prinv\_PRC (pat\_id,STUDY\_ID,pat\_enc\_csn\_id,proc\_date,proc\_code,proc\_type,PERFORMING\_PROV\_ID,PROC\_NAME)

SELECT distinct arpb.patient\_id AS pat\_id

,coh.study\_id

,arpb.pat\_enc\_csn\_id

,arpb.service\_date AS proc\_date

,arpb.cpt\_code AS proc\_code

,'CPT-Professional' AS proc\_type

,arpb.SERV\_PROVIDER\_ID AS PERFORMING\_PROV\_ID

,arpb.BILLING\_PROV\_ID AS BILLING\_PROV\_ID

,EAP.PROC\_NAME

FROM xdr\_prinv\_enc enc

JOIN xdr\_prinv\_coh coh ON enc.pat\_id = coh.pat\_id

JOIN arpb\_transactions arpb ON enc.pat\_enc\_csn\_id = arpb.pat\_enc\_csn\_id

LEFT JOIN clarity\_eap eap ON arpb.cpt\_code = eap.proc\_code

WHERE tx\_type\_c = 1 ----- Charges only

AND void\_date IS NULL;

COMMIT;

SELECT proc\_type, COUNT(\*),COUNT(DISTINCT pat\_id) FROM xdr\_prinv\_prc GROUP BY proc\_type;

/\*

CPT-Professional

CPT-Hospital

\*/

SELECT \* FROM xdr\_prinv\_prc;

INSERT INTO xdr\_prinv\_prc (pat\_id,STUDY\_ID,pat\_enc\_csn\_id,proc\_date,proc\_code,proc\_type,PERFORMING\_PROV\_ID,PROC\_NAME)

SELECT DISTINCT pat.pat\_id

,coh.study\_id

,px.pat\_enc\_csn\_id

,px.proc\_date

,px.px\_code AS proc\_code

,CASE

WHEN px.icd\_code\_set = 'ICD-9-CM Volume 3' THEN 'ICD-9'

WHEN px.icd\_code\_set = 'ICD-10-PCS' THEN 'ICD-10'

END AS proc\_type

,px.PROC\_PERF\_PROV\_ID

,px.procedure\_name AS proc\_name

FROM xdr\_prinv\_coh coh

JOIN i2b2.lz\_clarity\_procedures px ON pat.pat\_enc\_csn\_id = px.pat\_enc\_csn\_id;

COMMIT;

/\*

CPT-Professional

CPT-Hospital

ICD-9

ICD-10

\*/

# Database Cleanup/Queries

The following code sets up the drop table queries as well as calculate the current schema sizes.  Update requirements accordingly.

##### **Database Cleanup/Queries**

\-------------------------------------------------------------------------------

\-- Generate queries to drop tables for cleanup

\-------------------------------------------------------------------------------

\-- Drop tables based on owners/schemas/table names

select 'drop table ' || owner || '.' || t.table\_name || ' PURGE; --' || p.investigator sqlq

from all\_tables t

join i2b2.bip\_project p on substr(t.table\_name,5,6) = to\_char(p.project\_id)

WHERE t.owner in ('TTACORDA','CTSI\_RESEARCH','CKD')

AND ((not regexp\_like (table\_name,'+(drv|coh|bu|ctl|donotdelete)+','i'))

or (regexp\_like (table\_name,'+(subset)+','i')))

and t.table\_name like 'XDR%'

and (t.owner in ('TTACORDA','CKD')

or (t.owner = 'CTSI\_RESEARCH'

and lower(p.developer) in ('ehb','ttacorda','redcap2xdr')

)

)

order by sqlq

;

\-- Drop according to own tables sorted by descending size

select distinct 'drop table '

|| x.table\_name

|| ' PURGE; --'

|| z.SIZE\_MB

|| ' | ' || x.developer

|| ' | ' || x.investigator

|| ' | ' || x.irb

|| ' | ' || x.description

sqlq

,z.SIZE\_MB

from (select distinct to\_number(substr(t.table\_name,5,6)) project\_id

,t.owner || '.' || t.table\_name table\_name

,t.table\_name table\_name\_pre

,p.developer

,p.investigator

,p.irb

,p.description

from all\_tables t

join i2b2.bip\_project p on to\_number(substr(t.table\_name,5,6)) = p.project\_id

where t.owner = 'CTSI\_RESEARCH'

and substr(t.table\_name,1,4) = 'XDR\_'

and LENGTH(TRIM(TRANSLATE(substr(t.table\_name,5,6), '+-.0123456789',' '))) is null

) x

join (SELECT OBJECT\_NAME, OBJECT\_TYPE, IOT\_TYPE, SEGMENT\_NAME, (NVL(KB, 0) + NVL(KBIOT, 0)) /1024 AS SIZE\_MB

FROM

(

SELECT do.object\_name, do.object\_type, DT.IOT\_TYPE,

case when DT.IOT\_TYPE LIKE 'IOT%' THEN

DS\_IOT.SEGMENT\_NAME

ELSE DS.SEGMENT\_NAME

END AS SEGMENT\_NAME

, case when DT.IOT\_TYPE LIKE 'IOT%' THEN

CONS.INDEX\_NAME

ELSE NULL

END AS IOT\_INDEX\_NAME,

sum(DS.bytes)/1024 KB,

sum( case when DT.IOT\_TYPE LIKE 'IOT%' THEN

DS\_IOT.bytes/1024

else null end)

as KBIOT

FROM dba\_objects do

LEFT OUTER JOIN DBA\_segments DS

ON do.object\_name = DS.segment\_name

AND DO.OWNER = DS.OWNER

LEFT OUTER JOIN DBA\_TABLES DT

ON DO.OBJECT\_NAME = DT.TABLE\_NAME

AND DO.OWNER = DT.OWNER

LEFT OUTER JOIN all\_constraints cons

ON CONS.OWNER = DT.OWNER

AND cons.table\_name = DT.table\_name

AND cons.constraint\_type = 'P'

LEFT OUTER JOIN DBA\_segments DS\_IOT

ON CONS.INDEX\_NAME = DS\_IOT.segment\_name

AND DS\_IOT.OWNER = CONS.OWNER

where do.owner = 'CTSI\_RESEARCH'

AND do.object\_type = 'TABLE'

and do.object\_name like 'XDR%'

group by do.object\_name, do.object\_type, DT.IOT\_TYPE,

case when DT.IOT\_TYPE LIKE 'IOT%' THEN

DS\_IOT.SEGMENT\_NAME

ELSE DS.SEGMENT\_NAME

END

, case when DT.IOT\_TYPE LIKE 'IOT%' THEN

CONS.INDEX\_NAME

ELSE NULL

END

)

) z on x.table\_name\_pre = z.object\_name

where x.developer in ('TTACORDA','eHB')

and not regexp\_like(x.table\_name,'+(coh|drv)+','i')

order by z.SIZE\_MB desc --sqlq

;

\-------------------------------------------------------------------------------

\-- Calculate total allotted database sizes...

\-------------------------------------------------------------------------------

\-- ...for specific users

SELECT (sum(bytes)/1024/1024)\*0.0010 as gb\_size

FROM user\_segments us

JOIN all\_tables t ON us.segment\_name = t.table\_name

left join i2b2.bip\_project p on substr(t.table\_name,5,6) = to\_char(p.project\_id)

WHERE (t.owner in ('TTACORDA','CKD')

or (t.owner = 'CTSI\_RESEARCH'

and lower(p.developer) in ('ehb','ttacorda')

and p.project\_id is not null

)

)

;

\-- ...for specific schemas

SELECT (sum(bytes)/1024/1024)\*0.0010 as gb\_size --start: 259.60275 end: 244.0289375

FROM user\_segments us

JOIN all\_tables t ON us.segment\_name = t.table\_name

WHERE t.owner = 'CTSI\_RESEARCH'

;

\-- ...for my tables by size

SELECT OBJECT\_NAME, OBJECT\_TYPE, IOT\_TYPE, SEGMENT\_NAME, (NVL(KB, 0) + NVL(KBIOT, 0)) /1024 AS SIZE\_MB

FROM

(

SELECT do.object\_name, do.object\_type, DT.IOT\_TYPE,

case when DT.IOT\_TYPE LIKE 'IOT%' THEN

DS\_IOT.SEGMENT\_NAME

ELSE DS.SEGMENT\_NAME

END AS SEGMENT\_NAME

, case when DT.IOT\_TYPE LIKE 'IOT%' THEN

CONS.INDEX\_NAME

ELSE NULL

END AS IOT\_INDEX\_NAME,

sum(DS.bytes)/1024 KB,

sum( case when DT.IOT\_TYPE LIKE 'IOT%' THEN

DS\_IOT.bytes/1024

else null end)

as KBIOT

FROM dba\_objects do

LEFT OUTER JOIN DBA\_segments DS

ON do.object\_name = DS.segment\_name

AND DO.OWNER = DS.OWNER

LEFT OUTER JOIN DBA\_TABLES DT

ON DO.OBJECT\_NAME = DT.TABLE\_NAME

AND DO.OWNER = DT.OWNER

LEFT OUTER JOIN all\_constraints cons

ON CONS.OWNER = DT.OWNER

AND cons.table\_name = DT.table\_name

AND cons.constraint\_type = 'P'

LEFT OUTER JOIN DBA\_segments DS\_IOT

ON CONS.INDEX\_NAME = DS\_IOT.segment\_name

AND DS\_IOT.OWNER = CONS.OWNER

where do.owner = 'TTACORDA'

AND do.object\_type = 'TABLE'

group by do.object\_name, do.object\_type, DT.IOT\_TYPE,

case when DT.IOT\_TYPE LIKE 'IOT%' THEN

DS\_IOT.SEGMENT\_NAME

ELSE DS.SEGMENT\_NAME

END

, case when DT.IOT\_TYPE LIKE 'IOT%' THEN

CONS.INDEX\_NAME

ELSE NULL

END

)

ORDER BY OBJECT\_NAME

;

\-------------------------------------------------------------------------------

\-- Check for numeric values (example)

\-------------------------------------------------------------------------------

SELECT case

when regexp\_like(ord\_value,'+(\\.\\.)+','i') then ord\_value

when LENGTH(TRIM(TRANSLATE(ord\_value, '+-.0123456789',' '))) is null then 'value is numeric: ' || ord\_value

else ord\_value

end value\_is\_numeric

FROM i2b2.lz\_clarity\_labs

where rownum <= 1000

;

\-------------------------------------------------------------------------------

\-- Check code behind views

\-------------------------------------------------------------------------------

SELECT VIEW\_NAME, TEXT

FROM all\_VIEWS

where view\_name = 'V\_NOTES\_ROUTED';

\-------------------------------------------------------------------------------

\-- Set date formats for output

\-------------------------------------------------------------------------------

alter session set nls\_date\_format = 'MM/DD/YYYY HH24:MI';

\-------------------------------------------------------------------------------

\-- Analyze tables

\-------------------------------------------------------------------------------

ANALYZE TABLE i2b2.bip\_project VALIDATE STRUCTURE CASCADE;

\-------------------------------------------------------------------------------

\-- Check running queries

\-------------------------------------------------------------------------------

SELECT distinct s.SID

,s.serial#

,s.username

,s.osuser

,program

,s.status

,s.sql\_exec\_start

,sa.rows\_processed --will not change if query is hung up or in waiting status

,coalesce(sa.sql\_text,sq.sql\_text)

,s.sql\_id

,s.inst\_id

,s.event

,s.logon\_time

,sa.cpu\_time

,sa.elapsed\_time

,sa.runtime\_mem

FROM gv$session s

LEFT JOIN v$sqlarea sa ON s.sql\_id = sa.sql\_id

LEFT JOIN gv$sql sq ON s.sql\_id = sq.sql\_id

WHERE s.username IN ('TTACORDA','CKD','CTSI\_RESEARCH')

AND program in ('SQL Developer','sqlplus.exe')

and s.osuser = 'TheonaT1'

ORDER BY program desc, status, username DESC

,sql\_exec\_start

;

\-------------------------------------------------------------------------------

\-- Check sessions that have table locks

\-------------------------------------------------------------------------------

select a.sid, a.serial #

from v$session a, v$locked\_object b, dba\_objects c

where b.object\_id = c.object\_id

and a.sid = b.session\_id

and OBJECT\_NAME='LZ\_CLARITY\_PATIENT';

\-------------------------------------------------------------------------------

\-- Set privileges

\-------------------------------------------------------------------------------

\-- Revoke

select

'revoke all privileges ON ' || A.OWNER || '.' || A.TABLE\_NAME || ' from CTSI\_RESEARCH;'

ScriptIt

FROM ALL\_TABLES A

where a.owner = 'I2B2'

and lower(a.table\_name) like 'lz\_clarity%'

order by a.table\_name

;

\-- Grant

SELECT

'GRANT SELECT, INSERT, UPDATE, DELETE ON ' || A.OWNER || '.' || A.TABLE\_NAME || ' TO CTSI\_RESEARCH WITH GRANT OPTION;'

ScriptIt

FROM ALL\_TABLES A

WHERE A.OWNER = 'I2B2'

order by a.table\_name

;

\-------------------------------------------------------------------------------

\-- Copy from Rob's schema to ctsi\_research

\-------------------------------------------------------------------------------

select table\_name --35 rows found

from all\_tables

where owner = 'RFOLLETT'

and length(table\_name) > 26

;

select table\_name, length(table\_name) len --22 rows found, but max len=26

from all\_tables

where owner = 'RFOLLETT'

and substr(table\_name,1,4) <> 'XDR\_'

order by len desc

;

select distinct 'create table ctsi\_research.'

|| case

when table\_name like 'XDR%' then regexp\_replace(table\_name,'XDR\_','RFX\_',1,1)

else table\_name || '\_RFX'

end

|| ' as select \* from rfollett.'

|| table\_name

|| ';'

cr

from all\_tables

where owner = 'RFOLLETT'

order by cr

;

\-- run this, then export to a text file...

select case

when line = 1 then 'create or replace ' || text

else text

end

from all\_source

where owner = 'RFOLLETT'

order by name, type, line

;

\-------------------------------------------------------------------------------

\-- Get a random password

\-------------------------------------------------------------------------------

select dbms\_random.string('l',1) || dbms\_random.string('x',4) ||dbms\_random.string('l',1) ||dbms\_random.string('x',2) from dual;

\-------------------------------------------------------------------------------

\-- Get the timestamp for when a table was created

\-------------------------------------------------------------------------------

select created from user\_objects where lower(object\_name)='xdr\_123456\_demo';
```
## Care Everywhere Labs

###### (by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 9/22/20)

The following code allows you to find labs results received through the care Everywhere interface
``` sql
\------------------------------------------------

\-- Create table with all CE labs documents

\------------------------------------------------

CREATE TABLE XDR\_prinv\_ce\_docs as

select DISTINCT COH.PAT\_ID

,doc.DOC\_SOURCE\_ORG\_ID as organization\_id

,org.organization\_name

,DOC.DOCUMENT\_ID

,info.RESULT\_INST\_CMP\_DTTM

,info.RES\_PROC\_CMP\_ID -- this has been empty in my experience

,info.RES\_PROC\_NAME\_CMP

,info.RESULT\_KEY\_CMP

\--,eap.PROC\_NAME

\--,eap.proc\_code

from xdr\_prinv\_coh coh

JOIN DOCS\_RCVD doc ON coh.PAT\_ID = doc.PAT\_ID

join DOCS\_RCVD\_RES\_INFO info ON doc.DOCUMENT\_ID = info.DOCUMENT\_ID

join ORG\_DETAILS org on doc.DOC\_SOURCE\_ORG\_ID = org.ORGANIZATION\_ID

\--driver can be used here, or in some of other steps, since you might need to run this first without restrictions and run it by the PI

\--join xdr\_prinv\_CEdrv DRV ON info.RES\_PROC\_NAME\_CMP = drv.RES\_PROC\_NAME\_CMP

\-- and org.organization\_id= drv.organization\_id

\--There doesn't seem to be data to match here but just in case

\--left join CLARITY\_EAP eap ON info.RES\_PROC\_CMP\_ID = eap.PROC\_ID --RES\_PROC\_CMP\_ID is null, therefore it can't be match to clarity\_EAP

WHERE

\--Pull labs only

(doc.TYPE\_C = 8 --Lab Results

or

doc.DOC\_CONTENT\_TYPE\_C = 8);

\------------------------------------------------

\-- Create final table with all CE labs documents

\-- by adding the component name and the results, and applying the driver

\-- if it hasn't been applied yet

\-- (note: when I tried these two steps into one, performance was terrible)

\------------------------------------------------

CREATE TABLE XDR\_prinv\_ce\_labs as

select distinct doc.PAT\_ID

,doc.organization\_id

,doc.organization\_name

,DOC.DOCUMENT\_ID

,doc.RESULT\_INST\_CMP\_DTTM

\--,doc.RES\_PROC\_CMP\_ID -- this has been empty in my experience

,doc.RES\_PROC\_NAME\_CMP

,doc.RESULT\_KEY\_CMP

,res.RES\_COMP\_LOINC\_CMP -- this has been empty in my experience

,res.RES\_COMP\_NAME\_CMP

,res.RES\_VAL\_COMP

from XDR\_prinv\_ce\_docs doc

JOIN DOCS\_RCVD\_RES\_COMP res on doc.document\_id = res.document\_id

\--driver can be used here, or in some of other steps, since you might need to run this first without restrictions and run it by the PI

\--join xdr\_prinv\_CEdrv DRV ON doc.RES\_PROC\_NAME\_CMP = drv.RES\_PROC\_NAME\_CMP

and res.RES\_COMP\_NAME\_CMP = drv.RES\_COMP\_NAME\_CMP

and doc.organization\_id = drv.organization\_id

where res.RES\_COMP\_STAT\_CMP\_C = 4 --4 - completed

;

\------------------------------------------------

\-- Create driver table for PI

\------------------------------------------------

CREATE TABLE xdr\_prinv\_CEdrv AS

SELECT organization\_id

,organization\_name

,RES\_PROC\_CMP\_ID

,RES\_PROC\_NAME\_CMP

,RES\_PROC\_CMP\_ID

,RES\_PROC\_NAME\_CMP

FROM XDR\_prinv\_ce\_docs

,COUNT(\*)

GROUP BY organization\_id

,organization\_name

,RES\_PROC\_CMP\_ID

,RES\_PROC\_NAME\_CMP

,RES\_PROC\_CMP\_ID

,RES\_PROC\_NAME\_CMP

;

\------------------------------------------------

\-- Final data pull

\--It's possible that the results aren't compatible from site to site

\-- but this will require further investigation on a case by case basis

\------------------------------------------------

SELECT PAT\_ID

\--,DOCUMENT\_ID

,organization\_name

,RESULT\_INST\_CMP\_DTTM AS RESULT\_TIME

,RES\_PROC\_NAME\_CMP AS PROC\_NAME

,RES\_COMP\_NAME\_CMP AS COMPONENT\_NAME

,RES\_VAL\_COMP AS RESULTS

FROM XDR\_prinv\_ce\_labs lab

\--apply driver selection

join xdr\_prinv\_CEdrv DRV ON lab.RES\_PROC\_NAME\_CMP = drv.RES\_PROC\_NAME\_CMP

and lab.RES\_COMP\_NAME\_CMP = drv.RES\_COMP\_NAME\_CMP

and lab.organization\_id = drv.organization\_id

;
```
# Occupation Information

###### (by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 10/21/20)

The following code allows you to find a patient's occupation information (patient\_4.occupation\_c is not currently being used at the time of adding this script here)

##### **Occupation**

SELECT DISTINCT pat.pat\_id

,pt3.OCCUPATION

FROM xdr\_prinv\_pat coh

left join PATIENT\_3 pt3 ON pat.pat\_id = pt4.pat\_id

;

# Health Maintenance Topics

###### (by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 12/04/20)

The following code allows you to find information from the Health Maintenance panel for any given patient. This is the [link to Galaxy](https://uclabip.atlassian.net/wiki/spaces/WISE/pages/6455318/JIRA+reports) where to read about the Panel. And this is the link to learn about the table storing the data [\[F\_HM\_TREND\]](https://datahandbook.epic.com/ClarityDictionary/Details?tblName=F_HM_TREND)

Below is walkthrough of a case to look for Colonoscopy screenings

/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

Look up the topic name that you need

and use HM\_topic\_id in the next query

to identify your topic

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/

select \*

from CLARITY\_HM\_TOPIC;

/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

Plug the value from the previous step in QUALIFIED\_HMT\_ID

in the script below to obtain the most recent status for this topic

Modifier: By modifying the join, you can use a given date

and time threshold to look back at previous instances

(the QUALIFIED\_HMT\_ID below -7770000132-

pulls Colonoscopy preventive screening

status \[HMT\_DUE\_STATUS\_C\]

within 90 days of a date given by the PI \[date\_1\])

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/

SELECT X.pat\_id

,x.period

,x.extract\_date

,x.IDEAL\_RETURN\_DT

,x.due\_status

FROM (

select distinct coh.pat\_id

,'date\_1' as period

\--,coh.date\_2

,hm.extract\_date

,hm.TOPIC\_NAME

,hm.IDEAL\_RETURN\_DT

,zst.name as due\_status

,MAX(hm.extract\_date) OVER (PARTITION BY coh.pat\_id) AS latest\_measure

from xdr\_prinv\_coh coh

left JOIN F\_HM\_TREND hm ON coh.pat\_id = hm.pat\_id --and trunc(hm.extract\_date) <= coh.date\_1 + 90

left join ZC\_HMT\_DUE\_STATUS zst ON hm.HMT\_DUE\_STATUS\_C = zst.HMT\_DUE\_STATUS\_C

where

hm.QUALIFIED\_HMT\_ID = 770000132 --Colon Ca Screening: COLONOSCOPY

\-- 9 --Advanced Directive

) x

WHERE x.extract\_date = latest\_measure

;

# Questionnaires and Answers

(by [Robert Follett (Unlicensed)](https://uclabip.atlassian.net/wiki/people/557058:fb90de8e-f6e8-4a11-b471-2992e0b019af?ref=confluence) on 02/12/2021)

This code will pull questions and answers from MyChart questionnaires.

select distinct coh.study\_id

,coh.study\_csn

\--pefa.pat\_enc\_csn\_id

\-- ,pefa.pat\_id

,pefa.qf\_lqf\_id as form\_id

\-- ,pefa.qf\_hqa\_id as answer\_id

\-- ,qf.form\_name

,qf.pat\_frndly\_name

\-- ,cqa.quest\_id

,clqo.question

,cqa.line as ans\_line

,quest\_answer

,question\_instant as answer\_time

from pat\_enc\_form\_ans pefa

\---- Can join on csn or pat\_id

join YOUR\_COHORT coh on pefa.pat\_enc\_csn\_id = coh.pat\_enc\_csn\_id

\-- join YOUR\_COHORT coh on pefa.pat\_id = coh.pat\_id

join cl\_qform qf on pefa.qf\_lqf\_id = qf.form\_id

join cl\_qanswer\_qa cqa on pefa.qf\_hqa\_id = cqa.answer\_id

\--join cl\_qquest cqq on cqa.quest\_id = cqq.quest\_id -- question name

join cl\_qquest\_ovtm clqo on cqa.quest\_id = clqo.quest\_id -- question

where pefa.qf\_lqf\_id not in (78002) --- Insurance qestionnaire

order by coh.study\_id, pefa.qf\_lqf\_id, cqa.line;

# Address History (includes homeless)

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 3/25/2021)

This code will obtain address history per patient encounter. Flags are set if the patient is currently homeless or homeless at the visit.

select distinct enc.pat\_id

,enc.pat\_enc\_csn\_id

,enc.effective\_date\_dt

,pat.add\_line\_1

,case

when pat2.pat\_id is not null

then 1

else 0

end homeless\_currently

,pe4.pat\_homeless\_yn

,pahx.eff\_start\_date

,pahx.eff\_end\_date

,pahx.addr\_hx\_line1

,case

when pe4.pat\_enc\_csn\_id is not null

or pahx2.pat\_id is not null

then 1

else 0

end homeless\_at\_visit

from i2b2.lz\_clarity\_enc enc

left join patient pat on enc.pat\_id = pat.pat\_id

left join pat\_addr\_chng\_hx pahx on enc.pat\_id = pahx.pat\_id

and enc.effective\_date\_dt between pahx.eff\_start\_date

and pahx.eff\_end\_date

left join patient pat2 on pat.pat\_id = pat2.pat\_id

and ((pat.add\_line\_1 = pat2.add\_line\_1

and regexp\_like(pat2.add\_line\_1,'+(homeless)+','i')

)

or

(pat.city= pat2.city

and regexp\_like(pat2.city,'+(homeless)+','i')

)

)

left join pat\_enc\_4 pe4 on enc.pat\_enc\_csn\_id = pe4.pat\_enc\_csn\_id

and (nvl(pe4.pat\_homeless\_yn,'N') <> 'N')

left join pat\_addr\_chng\_hx pahx2 on pahx.pat\_id = pahx2.pat\_id

and pahx.addr\_hx\_line1 = pahx2.addr\_hx\_line1

and pahx.city\_hx = pahx2.city\_hx

and pahx.eff\_start\_date = pahx2.eff\_start\_date

and pahx.eff\_end\_date = pahx2.eff\_end\_date

and (regexp\_like(pahx2.addr\_hx\_line1,'+(homeless)+','i')

or regexp\_like(pahx2.city\_hx,'+(homeless)+','i')

)

order by enc.pat\_id, enc.effective\_date\_dt

;

# Cause of Death

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 4/5/2021)

This code will obtain patient cause of death.

\--Cause of Death during an admission

select distinct adm.pat\_id,

adm.pat\_enc\_csn\_id,

dpl10.code as icd\_code,

dpl10.icd\_desc

from i2b2.lz\_clarity\_enc adm --or your cohort

join patient\_3 p3 on adm.pat\_id = p3.pat\_id --(left join if entire cohort is needed)

left join edg\_current\_icd10 cit on p3.pcod\_cause\_dx\_id = cit.dx\_id

and cit.line = 1

left join i2b2.lz\_dx\_px\_lookup dpl10 on cit.code = dpl10.code

and dpl10.icd\_type = 10

and dpl10.code\_type = 'DX'

WHERE trunc(p3.PCOD\_INST\_REC\_DTTM) between trunc(adm.hosp\_admsn\_time)

and trunc(adm.hosp\_dischrg\_time);

\--Cause of Death for transplant donors: see Github Code Library (https://github.com/ctsidev/bipdata/blob/master/CodeLibrary/Transplant.sql)

# GFR labs

(by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 6/16/2021)

Code to calculate GFR results taking into account race. You will have to left join to patient table and lookup patient\_race\_c (2 = Black Afircan American)

where

(lab.component\_id not in (2452, 11539, 3000375, 10000661, 10010349, 2453, 11540, 3000376, 10000660, 10010348)

OR (lab.component\_id in (2452, 11539, 3000375, 10000661, 10010349) and race.patient\_race\_c <> 2)

OR (lab.component\_id in (2453, 11540, 3000376, 10000660, 10010348) and race.patient\_race\_c = 2)

# Kill Sessions

(by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 6/23/2021)

Code to find SQL Developer sessions open based on user’s schema, and statement to kill that session (this needs to be run by the DBA, but we can help them by providing this information).

select INST\_ID,SID,SERIAL#,USERNAME,STATUS,sql\_exec\_start,LOGON\_TIME

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

SELECT SESSION\_ID FROM DBA\_DML\_LOCKS WHERE NAME = 'OBSERVATION\_FACT';

SELECT SID, SERIAL# FROM V$SESSION WHERE SID IN (

SELECT SESSION\_ID FROM DBA\_DML\_LOCKS WHERE NAME = 'OBSERVATION\_FACT'

);

ALTER SYSTEM KILL SESSION '3669, 14655';

SELECT a.session\_id,

a.oracle\_username,

a.os\_user\_name,

b.owner,

b.object\_name,

b.object\_type,

DECODE(a.locked\_mode, 0, '0 - NONE: lock requested but not yet obtained',

1, '1 - NULL',

2, '2 - ROWS\_S (SS): Row Share Lock',

3, '3 - ROW\_X (SX): Row Exclusive Table Lock',

4, '4 - SHARE (S): Share Table Lock',

5, '5 - S/ROW-X (SSX): Share Row Exclusive Table Lock',

6, '6 - Exclusive (X): Exclusive Table Lock') LOCKED\_MODE

FROM v$locked\_object a,

dba\_objects b

Where A.Object\_Id = B.Object\_Id

and a.os\_user\_name = 'JavierS1';

# Recruitment Request Prioritization

(by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 7/16/2021)

Criteria to narrow down patient selection to meet disclousure criteria (count).

/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

1\. patients have the contact information field(s) completed

2\. patients had a completed UCLA encounter in the last 12 months

3\. After this, we will apply a randomization function and keep track of what patients have been sent to PI team already (and which ones are left to send)

Once PI team goes through the list and request more, we will send another release (ex 499).

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/

create table XDR\_PRINV\_COH\_recruit\_release as

SELECT distinct coh.ip\_patient\_id

,coh.pat\_fist\_name

,coh.pat\_last\_name

,coh.phone

,coh.addr\_line\_1

,coh.email

FROM XDR\_PRINV\_COH coh

left join i2b2.lz\_clarity\_enc enc on enc.pat\_id = coh.pat\_id

\-- UCLA encounter completed in the last 12 months

and enc.effective\_date\_dt between current\_time - 365.25 and current\_time

and ((enc.enc\_type\_c <> '5' --TT-4/11/2016: Exclude Canceled appointments

OR enc.appt\_status\_c is null or enc.appt\_status\_c = 2)

WHERE

\-- contant information available

(PHONE IS NOT NULL

ADDR\_LINE1 IS NOT NULL

AND EMAIL IS NOT NULL

)

and enc.pat\_id is not null

;

randomization (pending)

# Bone Marrow Transplant (BMT)

(by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 8/31/2021)

Code to find the BMT records.

select distinct coh.pat\_id

,ep.START\_DATE as tx\_date

,'Bone marrow' as tx\_name

from xdr\_prinv\_coh coh

join pat\_enc enc on coh.pat\_id = enc.pat\_id and coh.deleted\_yn is null

join Episode\_Link lnk on enc.pat\_enc\_csn\_id = lnk.pat\_enc\_csn\_id

join Episode ep on lnk.episode\_id = ep.episode\_id

left join BMT\_INFO bmt on ep.episode\_id = bmt.SUMMARY\_BLOCK\_ID

where --64 cases

lnk.SUM\_BLK\_TYPE\_ID = 2050001100 ----2050001100 "HSCT ALLOGENEIC RECIPIENT"

# Pregnancy/Delivery

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 9/10/2021)

Code to find pregnancy data.

\--------------------------------------------------------------------------------

\-- This query results in all pregnancies per patient.

\-- To check for "current" pregnancies, see where clause.

\--------------------------------------------------------------------------------

select distinct x.\*

from (select distinct coh.pat\_id

,dx.contact\_date dx\_diagnosis\_date

,dx2.effective\_date dx2\_diagnosis\_date

,round(months\_between(sysdate, dx.contact\_date),2) dx\_mos

,round(months\_between(sysdate, dx2.effective\_date),2) dx2\_mos

,lab.specimn\_taken\_time

,round(months\_between(sysdate, lab.specimn\_taken\_time),2) lab\_mos

,preg.pat\_id preg\_pat\_id

,prge.pat\_enc\_csn\_id preg\_pat\_enc\_csn\_id

,prge.effective\_date\_dt preg\_effective\_date\_dt

,round(months\_between(sysdate, prge.effective\_date\_dt),2) preg\_mos

,ob.mom\_id mom\_pat\_id

,ob.del\_dttm delivery\_date

,max(ob.del\_dttm) over (partition by coh.pat\_id) delivery\_date\_last

,round(months\_between(sysdate, ob.del\_dttm),2) ob\_mos

,ob.ga gestational\_age

,ob.delmeth\_c delivery\_method\_c

,zdt.name delivery\_method

from xdr\_prinv\_cohpat coh

left join i2b2.lz\_clarity\_diagnosis\_new dx on coh.pat\_id = dx.pat\_id

and ((dx.icd\_type = 9 --no need to check icd9 for current pregnancy

and trunc(dx.contact\_date) between to\_date('01/01/2013','mm/dd/yyyy')

and to\_date('09/30/2015','mm/dd/yyyy')

and dx.icd\_code between '630' and '679.9999999999'

)

or

(dx.icd\_type = 10

and trunc(dx.contact\_date) >= to\_date('10/01/2015','mm/dd/yyyy')

and regexp\_like(dx.icd\_code,'^(O|Z3(4|A|7))','i')

)

)

left join i2b2.int\_dx dx2 on coh.pat\_id = dx2.pat\_id

and trunc(dx2.effective\_date) between to\_date('01/01/2013','mm/dd/yyyy')

and to\_date('09/30/2015','mm/dd/yyyy')

and dx2.icd9\_code between '630' and '679.9999999999'

left join i2b2.bip\_encounter\_pat\_link enc on coh.pat\_id = enc.pat\_id

left join i2b2.lz\_clarity\_labs lab on enc.pat\_enc\_csn\_id = lab.pat\_enc\_csn\_id

and upper(lab.ord\_value) = 'POSITIVE' and lab.proc\_code in ('HIS2257','LAB144','HIS5747','LAB6256','HIS2256','HIS2258','POC1004','HIS2259','LAB437','OSL437','POC7')

\-- driver being used by Covid registry ETL

left join i2b2.xdr\_covid\_preg\_lab\_drv drv on lab.component\_id = drv.component\_id

left join clarity.v\_ob\_enc\_obgyn\_stat preg on coh.pat\_id = preg.pat\_id

and preg.obgyn\_stat\_c = 4 --pregnant at some point - for "current" pregnancies, see where clause below

left join i2b2.lz\_clarity\_enc prge on preg.pat\_enc\_csn\_id = prge.pat\_enc\_csn\_id

left join clarity.v\_ob\_del\_records ob on coh.pat\_id = ob.mom\_id

left join clarity.ZC\_DELIVERY\_TYPE zdt on ob.delmeth\_c = zdt.DELIVERY\_TYPE\_C

) x

\--the following where clause is for "current" pregnancies

\-- where (dx\_mos <= 9 --qualified for current pregnancy using dx codes (no need to check dx2 because icd-9 codes are before 10/1/2015)

\-- or lab\_mos <= 9 --qualified for current pregnancy using lab results

\-- or preg\_mos <= 9 --qualified for current pregnancy using ob data

\-- )

\-- and (ob\_mos is null --if currently pregnant, has not delivered in the past 9 months

\-- or ob\_mos > 9 --older deliveries

\-- )

;

\--------------------------------------------------------------------------------

\-- Notes:

\-- 1. This query obtains pregnancies based on encounters

\-- 2. Change the date criterion of 1/1/2013 per project.

\--------------------------------------------------------------------------------

select dx.pat\_enc\_csn\_id

from i2b2.lz\_clarity\_diagnosis\_new dx

where (dx.icd\_type = 9

and trunc(dx.contact\_date) between to\_date('01/01/2013','mm/dd/yyyy')

and to\_date('09/30/2015','mm/dd/yyyy')

and dx.icd\_code between '630' and '679.9999999999'

)

or (dx.icd\_type = 10

and trunc(dx.contact\_date) >= to\_date('10/01/2015','mm/dd/yyyy')

and regexp\_like(dx.icd\_code,'^(O|Z3(4|A|7))','i')

)

union

select dx.pat\_enc\_csn\_id

from i2b2.int\_dx dx

where trunc(dx.effective\_date) between to\_date('01/01/2013','mm/dd/yyyy')

and to\_date('09/30/2015','mm/dd/yyyy')

and dx.icd9\_code between '630' and '679.9999999999'

union

select lab.pat\_enc\_csn\_id

from i2b2.bip\_encounter\_pat\_link enc

join i2b2.lz\_clarity\_labs lab on enc.pat\_enc\_csn\_id = enc.pat\_enc\_csn\_id

\-- driver being used by Covid registry ETL

join i2b2.xdr\_covid\_preg\_lab\_drv drv on lab.component\_id = drv.component\_id

where upper(lab.ord\_value) = 'POSITIVE'

and lab.specimn\_taken\_time >= to\_date('01/01/2013','mm/dd/yyyy')

and lab.proc\_code in ('HIS2257','LAB144','HIS5747','LAB6256','HIS2256','HIS2258','POC1004','HIS2259','LAB437','OSL437','POC7')

union

select enc.pat\_enc\_csn\_id

from i2b2.lz\_clarity\_enc enc

join clarity.v\_ob\_enc\_obgyn\_stat preg on enc.pat\_enc\_csn\_id = preg.pat\_enc\_csn\_id

where enc.effective\_date\_dt >= to\_date('01/01/2013','mm/dd/yyyy')

and preg.obgyn\_stat\_c = 4 --pregnant

;

\--------------------------------------------------------------------------------

\-- Notes:

\-- 1. This query obtains specific labs during a pregnancy

\-- 2. ep.ob\_wrk\_edd\_dt is the estimated due date

\--------------------------------------------------------------------------------

from xdr\_123456\_lab lab --lab cohort containing pat\_id

join i2b2.lz\_clarity\_enc e on lab.pat\_id = e.pat\_id

join episode\_link el on e.pat\_enc\_csn\_id = el.pat\_enc\_csn\_id

join episode ep on el.episode\_id = ep.episode\_id

and lab.specimn\_taken\_time between ep.ob\_wrk\_edd\_dt - 280 and ep.ob\_wrk\_edd\_dt --280=40 pregnancy weeks \* 7

# Calculate ICU stay times

###### (by @jsanz on 9/29/19)

This code will create a table that will calculate the start and end date/times of each ICU that happened during each encounter. If the patient went in and out of the ICU multiple times during the same encounter. Then the record will show multiple entries for that encounter, one per each episode. In order to calculate the aggregated total time that a patient spent in the ICU during a particular encounter, you can calculate the time from start to end, for each individual stay, and add them by encounter. (this last calculation is not in the code below).

\--------------------------------------

\-- create ADT table:

\-- this code is slightly different from the LZ ADT table

\-- becuase it brings in some additional variables that we use

\-- to identify ICU stays. Which can be detemrined by the department name

\-- or by the level of care provided to the patient

\----------------------------------------

CREATE TABLE xdr\_prinv\_adt AS

select distinct adt.pat\_id,

adt.pat\_enc\_csn\_id,

case when adt.event\_type\_c = 1 and adt.next\_out\_event\_id = enc2.hsp\_dis\_event\_id then 'Admit/Discharge'

when adt.event\_type\_c = 1 then 'Admit'

when adt.event\_type\_c = 3 and adt.next\_out\_event\_id = enc2.hsp\_dis\_event\_id then 'Discharge'

else 'Transfer' end as event\_type,

adt.event\_id as in\_event\_id,

adt.department\_id,

dept.department\_name,

dept.dept\_abbreviation,

dept.specialty,

dept.rev\_loc\_id,

loc.loc\_name,

adt.effective\_time as time\_in,

adtout.effective\_time as time\_out,

adt.next\_out\_event\_id as out\_event\_id,

enc2.hsp\_adm\_event\_id as adm\_event\_id,

enc2.hsp\_dis\_event\_id as dis\_event\_id,

enc.inpatient\_data\_id,

adt.PAT\_LVL\_OF\_CARE\_C,

adt.ACCOMMODATION\_C

from clarity\_adt adt

join xdr\_PRINV\_coh\_final pat on adt.pat\_id = pat.pat\_id

join pat\_enc enc on adt.pat\_enc\_csn\_id = enc.pat\_enc\_csn\_id

join pat\_enc\_hsp\_2 enc2 on adt.pat\_enc\_csn\_id = enc2.pat\_enc\_csn\_id

left join clarity\_adt adtout on adt.next\_out\_event\_id = adtout.event\_id

left join clarity\_dep dept on adt.department\_id = dept.department\_id

left join clarity\_loc loc on dept.rev\_loc\_id = loc.loc\_id

where adt.event\_type\_c in (1,3)

and adt.event\_subtype\_c != 2

and enc2.hsp\_adm\_event\_id is not null;

xdr\_covid\_icu\_times

\---

\--------------------------------------

\-- this procedure runs through the ADT table and calculate

\-- the start and end of each ICU that happened during each encounter

\-- if the patient went in and out of the ICU multiple times, then the

\-- record will show multiple entries for that encounter, with each episode

\----------------------------------------

\-- create this table to store the output

create table xdr\_prinv\_icu\_times

( "PAT\_ID" VARCHAR2(18 BYTE) COLLATE "USING\_NLS\_COMP",

"CSN" NUMBER(18,0) NOT NULL ENABLE,

"ICU\_START" DATE,

"ICU\_END" DATE

);

I think that this code should run

\--procedure covid\_icu as

v\_procname varchar2(120) := 'covid\_charlson';

begin

i2b2.do\_the\_truncate('i2b2.xdr\_covid\_icu\_times');

DECLARE

cursor c\_icu is

select adt.pat\_id

,adt.pat\_enc\_csn\_id

,adt.time\_in as icu\_in

,adt.time\_out as icu\_out

from xdr\_prinv\_adt adt

left join ZC\_LVL\_OF\_CARE zloc on adt.PAT\_LVL\_OF\_CARE\_C = zloc.LEVEL\_OF\_CARE\_C

left join ZC\_ACCOMMODATION za on adt.ACCOMMODATION\_C = za.ACCOMMODATION\_C

where department\_name like '%ICU%'

OR ( adt.PAT\_LVL\_OF\_CARE\_C IS NOT NULL AND adt.PAT\_LVL\_OF\_CARE\_C = '6') -- ICU

OR adt.ACCOMMODATION\_C IN (25,28,46)

order by adm.pat\_id, adm.csn, adt.time\_in;

t\_rec c\_icu%rowtype;

hold\_enc number := 0;

hold\_pat varchar2(20);

hold\_start\_time date;

hold\_end\_time date;

BEGIN

OPEN c\_icu;

LOOP

FETCH c\_icu into t\_rec;

EXIT WHEN c\_icu%notfound;

IF hold\_enc = 0 THEN -- first pass

hold\_enc := t\_rec.csn;

hold\_pat := t\_rec.pat\_id;

hold\_start\_time := t\_rec.icu\_in;

hold\_end\_time := t\_rec.icu\_out;

ELSIF hold\_enc = t\_rec.csn THEN -- same encounter

IF t\_rec.icu\_in = hold\_end\_time THEN --- if icu in = the last out time move the new out time to hold

hold\_end\_time := t\_rec.icu\_out;

else -- icu in != icu hold write a record

insert into xdr\_prinv\_icu\_times(pat\_id, csn, icu\_start, icu\_end)

VALUES (hold\_pat, hold\_enc, hold\_start\_time, hold\_end\_time);

hold\_start\_time := t\_rec.icu\_in;

hold\_end\_time := t\_rec.icu\_out;

END IF;

\-- hold\_enc != new enc, we have a new telemetry action -- write the old one

ELSE

insert into xdr\_prinv\_icu\_times(pat\_id, csn, icu\_start, icu\_end)

VALUES (hold\_pat, hold\_enc, hold\_start\_time, hold\_end\_time);

hold\_enc := t\_rec.csn;

hold\_pat := t\_rec.pat\_id;

hold\_start\_time := t\_rec.icu\_in;

hold\_end\_time := t\_rec.icu\_out;

END IF;

END LOOP;

\-- process last record and calculate

insert into xdr\_prinv\_icu\_times(pat\_id, csn, icu\_start, icu\_end)

VALUES (hold\_pat, hold\_enc, hold\_start\_time, hold\_end\_time);

CLOSE c\_icu;

END;

commit;

\-- end covid\_icu;

# Glasgow Comma Score (GCS)

###### (by @jsanz on 9/29/19)

The **Glasgow** Coma Scale/**Score** (GCS) estimates coma severity based on Eye (4), Verbal (5), and Motor (6) criteria.. The codes below will help you locate the GCS in your flowsheets table. APply them to the second table that you build.

| flo_meas_id | flo_meas_name |
| --- | --- |
| 16011292 | Glasgow Coma Scale Score |
| 160348 | Glasgow Coma Scale Score |
| 160351 | Glasgow Coma Scale Score |
| 3040101407 | Glasgow Coma: Age |
| 3040401001 | Glasgow Coma Scale Score |
| 401001 | Glasgow Coma Scale Score |

# Sequential Organ Failure Assessment (**SOFA**)

###### (by @jsanz on 9/29/19)

The Sequential Organ Failure Assessment (**SOFA**) Score is a mortality prediction score that is based on the degree of dysfunction of six organ systems. The score is calculated on admission and every 24 hours until discharge using the worst parameters measured during the prior 24 hours.  The code below will pull the SOFA score from your flowsheets table. Apply them to the second table that you build.

| FLT_ID | TEMPLATE_NAME |
| --- | --- |
| 3040100960 | IP SOFA UCLA |

# DENSE RANK in Oracle

by [Cenan Pirani](https://uclabip.atlassian.net/wiki/people/5f5922bcff2fe2006fb700d8?ref=confluence) on 10/4/2021

### How to pull first value in a query based on order rank

In the example below of LABS we are looking for the most recent labs available per patient. Since labs can be spread out over many dates DENSE RANK allows you to GROUP by patient and component, and then select the first value if the ‘order\_time’ is sorted descending per grouped ‘pat\_id’.

Example:

select pat\_id

,component\_name

,max(ord\_value) keep (dense\_rank first order by order\_time desc) as lab\_value

,max(reference\_unit) keep (dense\_rank first order by order\_time desc) as lab\_value\_unit

from xdr\_100001\_lab

group by pat\_id

,component\_name

order by pat\_id

,component\_name

;

# Pathology HonestBroker Accession Numbers

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 10/19/2021)

Code to process accession numbers for pathology. This will deidentify accession numbers as well as identify result texts that contain the literals “block” or “cassette”.

\----------------------------------------------------------------------------------------------------------------------

\-- After creating pathology (entity\_cd=PATH) and pathology\_results (entity\_cd=PATHR) files, complete the following:

\----------------------------------------------------------------------------------------------------------------------

\--------------------------------------------------------------------------------

\-- Create a new pathology file to encrypt pathology accession number

\--------------------------------------------------------------------------------

\-- Create a table with deidentified accession numbers

drop table xdr\_123456\_path\_accnum purge;

create table xdr\_123456\_path\_accnum as

select distinct demoi.mrn

,min(i2b2.f\_get\_deid\_new(6,path.accession\_number,113164)) over (partition by demoi.study\_id, path.accession\_number) ip\_accession\_number

,path.\*

from xdr\_123456\_path path

join xdr\_123456\_demoi demoi on path.study\_id = demoi.study\_id

;

\-- Ensure this table's counts are identical to the original

select count(\*) from xdr\_123456\_path\_accnum;

select count(\*) from xdr\_123456\_path;

\-- Ensure the following counts are identical to verify uniqueness

select count(distinct accession\_number), count(distinct ip\_accession\_number) from xdr\_123456\_path\_accnum;

\--------------------------------------------------------------------------------

\-- Add a column to the pathology results to identify if a result text line

\-- contains "block" or "cassette" and update

\--------------------------------------------------------------------------------

alter table xdr\_123456\_pathr add block\_cassette\_in\_txt varchar(1);

update xdr\_123456\_pathr

set block\_cassette\_in\_txt = 'Y'

where regexp\_like(result\_txt, 'cassette|block', 'i')

;

commit;

\--------------------------------------------------------------------------------

\-- Respool pathology files

\-- Example of respool:

\-- Refer to E:\\Projects\\108564\\Execute\_Custom.bat and PullMain\_Custom.sql

\--------------------------------------------------------------------------------

\--spool e:\\projects\\123456\\Data\\Pathology.txt

select 'ip\_patient\_id,ip\_enc\_id,ip\_order\_proc\_id,ip\_accession\_number,order\_time,result\_time,procedure\_description,specimen\_source,specimen\_type,specimen\_taken\_time' export from dual union all

select '"' || study\_id || '"' || ',' || '"' || study\_csn || '"' || ',' || '"' || study\_order\_proc\_id || '"' || ',' || '"' || ip\_accession\_number || '"' || ',' || '"' || order\_time

|| '"' || ',' || '"' || result\_time || '"' || ',' || '"' || procedure\_description || '"' || ',' || '"' || specimen\_source || '"' || ',' || '"' || specimen\_type

|| '"' || ',' || '"' || specimen\_taken\_time || '"' export

from (select distinct study\_id, study\_csn, study\_order\_proc\_id, ip\_accession\_number, order\_time, result\_time, procedure\_description, specimen\_source, specimen\_type

, specimen\_taken\_time

from xdr\_123456\_path\_accnum

order by study\_id, study\_order\_proc\_id

);

\--spool off

\--spool e:\\projects\\123456\\Data\\Pathology\_Results.txt

select 'ip\_patient\_id,ip\_order\_proc\_id,component\_id,component\_name,sort\_key,block\_cassette\_in\_txt,result\_txt' export from dual union all

select '"' || study\_id || '"' || ',' || '"' || study\_order\_proc\_id || '"' || ',' || '"' || component\_id || '"' || ',' || '"' || component\_name

|| '"' || ',' || '"' || sort\_key || '"' || ',' || '"' || block\_cassette\_in\_txt || '"' || ',' || '"' || result\_txt || '"' export

from (select distinct study\_id, study\_order\_proc\_id, component\_id, component\_name, sort\_key, block\_cassette\_in\_txt, result\_txt

from xdr\_123456\_pathr

order by study\_id, study\_order\_proc\_id, component\_id, sort\_key

);

\--spool off

\--------------------------------------------------------------------------------

\-- Do not output this to Data subfolder because this is sent to Clara Magyar, not the PI

\--------------------------------------------------------------------------------

\--spool e:\\projects\\123456\\Pathology\_Cases.txt

select 'ip\_patient\_id,mrn,case\_number,ip\_case\_number' export from dual union all

select '"' || study\_id || '"' || ',' || '"' || mrn || '"' || ',' || '"' || accession\_number || '"' || ',' || '"' || ip\_accession\_number || '"' export

from (select distinct study\_id, mrn, accession\_number, ip\_accession\_number

from xdr\_123456\_path\_accnum

where accession\_number is not null

order by mrn, accession\_number

);

\--spool off

\--------------------------------------------------------------------------------

\-- Rerun Data Dictionary (See E:\\Projects\\123456\\Execute\_Custom.bat)

\--------------------------------------------------------------------------------

# Imaging HonestBroker Accession Numbers

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 11/5/2021)

Code to process accession numbers for imaging. This will deidentify accession numbers as well as provide modality and list\_order\_number (sequential number starting with 1).

\----------------------------------------------------------------------------------------------------------------------

\-- After creating imaging (entity\_cd=IMG) files, complete the following:

\----------------------------------------------------------------------------------------------------------------------

\--------------------------------------------------------------------------------

\-- Create a new imaging file to encrypt imaging accession number

\-- and obtain other requested variables

\--------------------------------------------------------------------------------

\-- Create a table with deidentified accession numbers

drop table xdr\_123456\_img\_accnum purge;

create table xdr\_123456\_img\_accnum as

select distinct rownum list\_order\_number

,demoi.pat\_mrn\_id mrn

,i2b2.f\_get\_deid\_new(5,img.accession\_number,123456) ip\_accession\_number

,substr(img.procedure\_name,1,instr(procedure\_name,' ') - 1) modality --get modality up to first space

,img.\*

from xdr\_123456\_img img

join xdr\_123456\_coh\_patonly cpo on img.study\_id = cpo.study\_id

join i2b2.lz\_clarity\_patient demoi on cpo.pat\_id = demoi.pat\_id

;

\-- Ensure this table's counts are identical to the original

select count(\*) from xdr\_123456\_img\_accnum;

select count(\*) from xdr\_123456\_img;

\-- Ensure the following counts are identical to verify uniqueness

select count(distinct accession\_number), count(distinct ip\_accession\_number) from xdr\_123456\_img\_accnum;

\--------------------------------------------------------------------------------

\-- Respool imaging files

\-- Example of respool:

\-- Refer to E:\\Projects\\108784\\Execute\_Custom.bat and PullMain\_Custom.sql

\--------------------------------------------------------------------------------

\--spool e:\\projects\\123456\\Data\\Imaging.txt

select 'ip\_patient\_id,ip\_enc\_id,ip\_order\_proc\_id,procedure\_id,procedure\_name,ip\_accession\_number,modality' export from dual union all

select '"' || study\_id || '"' || ',' || '"' || study\_csn || '"' || ',' || '"' || study\_order\_proc\_id

|| '"' || ',' || '"' || procedure\_id || '"' || ',' || '"' || procedure\_name

|| '"' || ',' || '"' || ip\_accession\_number || '"' || ',' || '"' || modality || '"' export

from (select distinct study\_id, study\_csn, study\_order\_proc\_id, procedure\_id

, procedure\_name, ip\_accession\_number, modality

from xdr\_123456\_img\_accnum

order by study\_id, study\_order\_proc\_id);

\--spool off

\--------------------------------------------------------------------------------

\-- Do not output this to Data subfolder because this is sent to DICOM, not the PI

\-- Spool e:\\projects\\123456\\Imaging\_UleadFolderName.txt (e.g. Imaging\_Jamshidi\_21\_10-001869.txt)

\-- Copy to Box folder Imaging\_Accession\_Numbers

\--------------------------------------------------------------------------------

\--spool E:\\Projects\\123456\\Sent\_Backup\\Imaging\_UleadFolderName.txt

select 'list\_order\_number,mrn,ip\_patient\_id,accession\_number,ip\_accession\_number,modality' export from dual union all

select '"' || list\_order\_number || '"' || ',' || '"' || mrn || '"' || ',' || '"' || study\_id

|| '"' || ',' || '"' || accession\_number || '"' || ',' || '"' || ip\_accession\_number

|| '"' || ',' || '"' || modality

|| '"' export

from (select distinct \*

from xdr\_123456\_img\_accnum

where accession\_number is not null

and ((LENGTH(TRIM(TRANSLATE(accession\_number, '+-.0123456789',' '))) is null --numeric accession\_number

and accession\_number like '4%'

and length(accession\_number) = 8

)

or

modality = 'ECG'

)

order by list\_order\_number

);

\--spool off

\--------------------------------------------------------------------------------

\-- Rerun Data Dictionary (See E:\\Projects\\108784\\Execute\_Custom.bat)

\--------------------------------------------------------------------------------

# Changing IDs and MRNs

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 11/5/2021)

Code to process changing IDs and MRNs. Please refer to ctsi\_research.f\_get\_curr\_pt. Input variables are pi\_type and pi\_pat and Output variable is the status and new value. Please see below.

\--------------------------------------------------------------------------------

\-- Please refer to ctsi\_research.f\_get\_curr\_pt(sample of Input/output

\-- parameters below and note necessary requirements in comments)

\--------------------------------------------------------------------------------

create or replace function f\_get\_curr\_pt

(pi\_type in integer --1=pat\_id, 2=mrn

,pi\_pat in varchar2 --pat\_id/mrn to be validated

)

return varchar2 --returns status + new value (if applicable)

\--status: 'C'=CURRENT, 'N'=NEW PT, 'X'=EXCLUDE, ,'O'=OPTED OUT OF RESEARCH, 'F'=NOT FOUND, 'E'=ERROR

\--note that 'X'=EXCLUDE could mean "restricted/test/dummy"

\--note that 'E'=ERROR will also return the sql error message ilo new value

\--------------------------------------------------------------------------------

\-- Get new id examples

\--------------------------------------------------------------------------------

select f\_get\_curr\_pt(1,'Z000001') from dual;

select f\_get\_curr\_pt(1,pat\_id) from i2b2.lz\_clarity\_patient where rownum = 1;

\--------------------------------------------------------------------------------

\-- Get new id for a file

\--------------------------------------------------------------------------------

select distinct x.\*

,i2b2.f\_death\_clarity\_all(coalesce(trim(substr(x.new\_pat\_id\_info,2)),x.clarity\_pat\_id)) f\_death\_info

,substr(x.new\_pat\_id\_info,1,1) new\_pat\_id\_stat

,coalesce(x.research\_opt\_out\_pre, p3.research\_opt\_out) research\_opt\_out

,coalesce(trim(substr(x.new\_pat\_id\_info,2)),x.clarity\_pat\_id) new\_pat\_id

from (select distinct coh.\*

,p.pat\_id lz\_pat\_id

,p2.pat\_id clarity\_pat\_id

,case when p2.pat\_id is not null then ' ' || p2.pat\_id

else f\_get\_curr\_pt(1,coh.pat\_id)

end new\_pat\_id\_info

,p.research\_opt\_out research\_opt\_out\_pre

from xdr\_123456\_cohpatfin coh

left join i2b2.lz\_clarity\_patient p on coh.pat\_id = p.pat\_id

left join clarity.patient p2 on coh.pat\_id = p2.pat\_id

left join i2b2.bip\_project\_disclosure bpd on coh.pat\_id = bpd.pat\_id

and bpd.project\_id = 109826

where bpd.pat\_id is null

) x

left join i2b2.lz\_clarity\_patient p3 on coalesce(trim(substr(x.new\_pat\_id\_info,2)),x.clarity\_pat\_id) = p3.pat\_id

;

# Control Cohort Identification

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 11/5/2021)

Code to create controls for a case cohort.

\--------------------------------------------------------------------------------

\-- Please refer to ctsi\_research.p\_create\_control (sample of Input/output

\-- parameters below and note necessary requirements in comments)

\--

\-- Alternate method to p\_create\_controls above:

\-- C:\\server\_apps\\eHonestBroker\\Create\_Controls.py

\-- pi\_project\_id = 123456

\-- pi\_max\_number\_matches = n, where n=integer of how many controls are requested per case

\--------------------------------------------------------------------------------

create or replace PROCEDURE P\_CREATE\_CONTROL

(pi\_project\_id IN i2b2.bip\_project.project\_id%TYPE --Must have a cohort-to-control file that exists in ctsi\_research schema, which includes coh\_pat\_id, ctl\_pat\_id, and rank

\--named xdr\_123456\_ctlpre, where 123456 is the project\_id

\--rank must be precoded in the order that PI requests, if any.

\--i.e. closest cosine\_similarity first

\-- see I:\\\_BIP\\Consults\\zz Completed\\Hser - Zhu (refresh)\\Hser\_Script\_Control.sql (Elixhauser)

\-- or I:\\\_BIP\\Consults\\Bui\_CRRT\\Bui\_CRRT\_Script.sql (Charlson) for examples

,pi\_max\_number\_matches IN INTEGER

) IS

\--------------------------------------------------------------------------------

\-- Step 1: Match case to control (must contain variables to match (e.g. age, sex)

\-- Input: a. Case table

\-- b. Preliminary control table

\-- Output: xdr\_123456\_ctlpre

\-- Note that rank is only necessary when there is a priority in matching. If

\-- there is no priority, this can be set to a sequential number per patient.

\--------------------------------------------------------------------------------

drop table xdr\_123456\_ctlpre purge;

create table xdr\_123456\_ctlpre as

select distinct cohcase.pat\_id coh\_pat\_id

,cohctl.pat\_id ctl\_pat\_id

,cohcase.match\_age age

,cohcase.sex sex

,cohcase.race race

,cohcase.ethnicity ethnicity

,cohctl.number\_of\_pl\_icds

,rank() over (partition by cohcase.pat\_id order by cohcase.pat\_id, cohctl.number\_of\_pl\_icds, cohctl.pat\_id) rank

from xdr\_123456\_coh\_patonly\_age cohcase -- This is the file that contains the case information

join xdr\_123456\_ctlprefin cohctl on cohcase.match\_age = cohctl.match\_age -- This is the file that contains the preliminary control information

and cohcase.sex = cohctl.sex

and cohcase.race = cohctl.race

and cohcase.ethnicity = cohctl.ethnicity

;

\--------------------------------------------------------------------------------

\-- Step 2: Get final case to control cohort

\-- Input: xdr\_123456\_ctlpre from Step 1

\-- Output: xdr\_123456\_case2ctl

\-- NOTE: In the event that PI wants further matching using cosine

\-- similarity, please jump to "Rank by cosine similarity"

\--------------------------------------------------------------------------------

\-- parameters: project\_id, #controls to case match

exec p\_create\_control(123456,2);

select count(\*), count(distinct coh\_pat\_id), count(distinct ctl\_pat\_id) from xdr\_123456\_case2ctl; --17829 9085 17829

select \* from xdr\_123456\_case2ctl order by coh\_pat\_id, ctl\_pat\_id;

/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

Rank by cosine similarity (example only - ignore specific project counts and comments)

E.g. Closest cosine\_similarity first - for further examples, see:

I:\\\_BIP\\Consults\\zz Completed\\Hser - Zhu (refresh)\\Hser\_Script\_Control.sql (Elixhauser)

\- 1 case to multiple (e.g. 2) controls

I:\\\_BIP\\Consults\\Bui\_CRRT\\Bui\_CRRT\_Script.sql (Charlson)

\- 1 case to 1 control

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/

\--------------------------------------------------------------------------------

\-- Get current demographics on case cohort

\--------------------------------------------------------------------------------

drop table xdr\_123456\_cohpat2 purge;

create table xdr\_123456\_cohpat2 as

select distinct coh.pat\_id

,p.sex

,p.mapped\_race\_name race

,p.ethnic\_group ethnicity

,case

when x2.death\_date\_derived = to\_date('01/01/0001','mm/dd/yyyy') then null

when x2.death\_date\_derived <> to\_date('01/01/0001','mm/dd/yyyy') then trunc(months\_between(x2.death\_date\_derived, x2.birth\_date)/12)

else trunc(months\_between(sysdate, x2.birth\_date)/12)

end current\_or\_death\_age

,case

when x2.death\_date\_derived is not null then 'Known Deceased'

else 'Not Known Deceased'

end vital\_status

,case

when x2.death\_date\_derived is not null then x2.death\_date\_derived

else null

end death\_datetime

,p2.pat\_name

,p.restricted\_yn

,p.pat\_mrn\_id mrn

,p.birth\_date

,p.research\_opt\_out

from xdr\_123456\_cohpat coh

join i2b2.lz\_clarity\_patient p on coh.pat\_id = p.pat\_id

join clarity.patient p2 on coh.pat\_id = p2.pat\_id

join (select x1.\*

,case

when (dead\_pat = 1 or dead\_enc = 1 or dead\_death = 1) and death\_date\_min = to\_date('12/31/9999','mm/dd/yyyy') then to\_date('01/01/0001','mm/dd/yyyy')

when (dead\_pat = 1 or dead\_enc = 1 or dead\_death = 1) and death\_date\_min <> to\_date('12/31/9999','mm/dd/yyyy') then death\_date\_min

else null

end death\_date\_derived

from (select distinct x.\*

,least(nvl(death\_date\_pat,to\_date('12/31/9999','mm/dd/yyyy'))

,nvl(death\_date\_enc,to\_date('12/31/9999','mm/dd/yyyy'))

,nvl(death\_date\_death,to\_date('12/31/9999','mm/dd/yyyy'))

) death\_date\_min

from (select coh.pat\_id

,coh.pat\_mrn\_id

,coh.ip\_patient\_id

,coh.birth\_date

,pat.pat\_status\_c

,ps.name as patient\_status

,case when pat.pat\_status\_c = 2 or pat.death\_date is not null then 1 else 0 end dead\_pat

,case when enc.pat\_id is not null then 1 else 0 end dead\_enc

,case when death.pat\_id is not null then 1 else 0 end dead\_death

,case when nvl(pat.death\_date,to\_date('01/01/0001','mm/dd/yyyy')) >= pat.birth\_date then pat.death\_date else null end death\_date\_pat

,max(enc.hosp\_disch\_time) over (partition by enc.pat\_id) death\_date\_enc

,case when nvl(death.death\_date,to\_date('01/01/0001','mm/dd/yyyy')) >= pat.birth\_date then death.death\_date else null end death\_date\_death

from xdr\_123456\_cohpat coh

join clarity.patient pat on coh.pat\_id = pat.pat\_id

left join clarity.pat\_enc\_hsp enc on pat.pat\_id = enc.pat\_id and (enc.disch\_disp\_c in ('20', '40', '41', '42', '71') or enc.ed\_disposition\_c = '8')

left join i2b2.lz\_death death on pat.pat\_id = death.pat\_id

left join clarity.zc\_patient\_status ps on pat.pat\_status\_c = ps.patient\_status\_c

) x

) x1

) x2 on coh.pat\_id = x2.pat\_id

;

alter table xdr\_123456\_cohpat2 add constraint xdr\_123456\_cohpat2\_pk primary key (pat\_id);

select count(\*), count(distinct pat\_id) from xdr\_123456\_cohpat2; --2462 2462

select count(\*), count(distinct pat\_id) from xdr\_123456\_cohpat; --2464 2464

select \* from xdr\_123456\_cohpat2;

\--------------------------------------------------------------------------------

\-- Get Charlson comorbidities for case cohort - Final case cohort

\--------------------------------------------------------------------------------

drop table xdr\_123456\_cohpat3 purge;

create table xdr\_123456\_cohpat3 as

select distinct charl.\*

,coh.ethnicity

,coh.race

,coh.sex

,coh.vital\_status

,coh.current\_or\_death\_age

from xdr\_123456\_cohpat2 coh

join i2b2.lz\_clarity\_charlson\_score charl on coh.pat\_id = charl.pat\_id

;

select count(\*), count(distinct pat\_id) from xdr\_123456\_cohpat3; --2392 2392

\--------------------------------------------------------------------------------

\-- Get initial controls

\--------------------------------------------------------------------------------

drop table xdr\_123456\_ctlpat1 purge;

create table xdr\_123456\_ctlpat1 as

select distinct p.pat\_id

,p.sex

,p.mapped\_race\_name race

,p.ethnic\_group ethnicity

,case

when x2.death\_date\_derived = to\_date('01/01/0001','mm/dd/yyyy') then null

when x2.death\_date\_derived <> to\_date('01/01/0001','mm/dd/yyyy') then trunc(months\_between(x2.death\_date\_derived, x2.birth\_date)/12)

else trunc(months\_between(sysdate, x2.birth\_date)/12)

end current\_or\_death\_age

,case

when x2.death\_date\_derived is not null then 'Known Deceased'

else 'Not Known Deceased'

end vital\_status

,case

when x2.death\_date\_derived is not null then x2.death\_date\_derived

else null

end death\_datetime

,p2.pat\_name

,p.restricted\_yn

,p.pat\_mrn\_id mrn

,p.birth\_date

,p.research\_opt\_out

from i2b2.lz\_clarity\_patient p

join clarity.patient p2 on p.pat\_id = p2.pat\_id

join (select x1.\*

,case

when (dead\_pat = 1 or dead\_enc = 1 or dead\_death = 1) and death\_date\_min = to\_date('12/31/9999','mm/dd/yyyy') then to\_date('01/01/0001','mm/dd/yyyy')

when (dead\_pat = 1 or dead\_enc = 1 or dead\_death = 1) and death\_date\_min <> to\_date('12/31/9999','mm/dd/yyyy') then death\_date\_min

else null

end death\_date\_derived

from (select distinct x.\*

,least(nvl(death\_date\_pat,to\_date('12/31/9999','mm/dd/yyyy'))

,nvl(death\_date\_enc,to\_date('12/31/9999','mm/dd/yyyy'))

,nvl(death\_date\_death,to\_date('12/31/9999','mm/dd/yyyy'))

) death\_date\_min

from (select pat.pat\_id

,pat.pat\_mrn\_id

,pat.birth\_date

,pat.pat\_status\_c

,ps.name as patient\_status

,case when pat.pat\_status\_c = 2 or pat.death\_date is not null then 1 else 0 end dead\_pat

,case when enc.pat\_id is not null then 1 else 0 end dead\_enc

,case when death.pat\_id is not null then 1 else 0 end dead\_death

,case when nvl(pat.death\_date,to\_date('01/01/0001','mm/dd/yyyy')) >= pat.birth\_date then pat.death\_date else null end death\_date\_pat

,max(enc.hosp\_disch\_time) over (partition by enc.pat\_id) death\_date\_enc

,case when nvl(death.death\_date,to\_date('01/01/0001','mm/dd/yyyy')) >= pat.birth\_date then death.death\_date else null end death\_date\_death

from clarity.patient pat

left join clarity.pat\_enc\_hsp enc on pat.pat\_id = enc.pat\_id and (enc.disch\_disp\_c in ('20', '40', '41', '42', '71') or enc.ed\_disposition\_c = '8')

left join i2b2.lz\_death death on pat.pat\_id = death.pat\_id

left join clarity.zc\_patient\_status ps on pat.pat\_status\_c = ps.patient\_status\_c

) x

) x1

) x2 on p.pat\_id = x2.pat\_id

;

alter table xdr\_123456\_ctlpat1 add constraint xdr\_123456\_ctlpat1\_pk primary key (pat\_id);

select count(\*), count(distinct pat\_id) from xdr\_123456\_ctlpat1; --6052064 6052064

select \* from xdr\_123456\_ctlpat1;

\--------------------------------------------------------------------------------

\-- Get Charlson comorbidities for controls

\--------------------------------------------------------------------------------

drop table xdr\_123456\_ctlpat2 purge;

create table xdr\_123456\_ctlpat2 as

select distinct charl.\*

,coh.ethnicity

,coh.race

,coh.sex

,coh.vital\_status

,coh.current\_or\_death\_age

from xdr\_123456\_ctlpat1 coh

join i2b2.lz\_clarity\_charlson\_score charl on coh.pat\_id = charl.pat\_id

left join xdr\_123456\_cohpat cs on coh.pat\_id = cs.pat\_id --use original 2464-pt cohort to ensure none are used in controls

where cs.pat\_id is null

;

select count(\*), count(distinct pat\_id) from xdr\_123456\_ctlpat2; --696541 696541

select \* from xdr\_123456\_ctlpat2;

\--------------------------------------------------------------------------------

\-- Match case to control prior to cosine similarity comparison

\--------------------------------------------------------------------------------

drop table xdr\_123456\_cs2ctlpre purge;

create table xdr\_123456\_cs2ctlpre as

select distinct cohcase.pat\_id coh\_pat\_id

,cohctl.pat\_id ctl\_pat\_id

,cohcase.sex

,cohcase.race

,cohcase.ethnicity

,cohcase.current\_or\_death\_age age

,cohcase.char\_total\_score

from xdr\_123456\_cohpat3 cohcase

join xdr\_123456\_ctlpat2 cohctl on cohcase.sex = cohctl.sex

and cohcase.race = cohctl.race

and cohcase.ethnicity = cohctl.ethnicity

and cohcase.current\_or\_death\_age = cohctl.current\_or\_death\_age

;

select count(\*), count(distinct coh\_pat\_id), count(distinct ctl\_pat\_id) from xdr\_123456\_cs2ctlpre; --2295324 2372 426830

select \* from xdr\_123456\_cs2ctlpre;

\--------------------------------------------------------------------------------

\-- Rename the columns so I don't have to change each x\* reference in the standard cosine\_similarity code

\--------------------------------------------------------------------------------

alter table xdr\_123456\_cohpat3 rename column char\_myocardial\_infarction to x1;

alter table xdr\_123456\_cohpat3 rename column char\_chf to x2;

alter table xdr\_123456\_cohpat3 rename column char\_perivasc to x3;

alter table xdr\_123456\_cohpat3 rename column char\_cerebrovascular\_disease to x4;

alter table xdr\_123456\_cohpat3 rename column char\_dementia to x5;

alter table xdr\_123456\_cohpat3 rename column char\_chrnlung to x6;

alter table xdr\_123456\_cohpat3 rename column char\_conn\_tiss\_rheum\_disease to x7;

alter table xdr\_123456\_cohpat3 rename column char\_ulcer to x8;

alter table xdr\_123456\_cohpat3 rename column char\_mild\_liver\_disease to x9;

alter table xdr\_123456\_cohpat3 rename column char\_dm to x10;

alter table xdr\_123456\_cohpat3 rename column char\_dmcx to x11;

alter table xdr\_123456\_cohpat3 rename column char\_paraplegia\_hemiplegia to x12;

alter table xdr\_123456\_cohpat3 rename column char\_renal\_disease to x13;

alter table xdr\_123456\_cohpat3 rename column char\_cancer to x14;

alter table xdr\_123456\_cohpat3 rename column char\_mod\_or\_sev\_liver\_disease to x15;

alter table xdr\_123456\_cohpat3 rename column char\_metastatic\_carcinoma to x16;

alter table xdr\_123456\_cohpat3 rename column char\_aids\_hiv to x17;

\--

alter table xdr\_123456\_ctlpat2 rename column char\_myocardial\_infarction to x1;

alter table xdr\_123456\_ctlpat2 rename column char\_chf to x2;

alter table xdr\_123456\_ctlpat2 rename column char\_perivasc to x3;

alter table xdr\_123456\_ctlpat2 rename column char\_cerebrovascular\_disease to x4;

alter table xdr\_123456\_ctlpat2 rename column char\_dementia to x5;

alter table xdr\_123456\_ctlpat2 rename column char\_chrnlung to x6;

alter table xdr\_123456\_ctlpat2 rename column char\_conn\_tiss\_rheum\_disease to x7;

alter table xdr\_123456\_ctlpat2 rename column char\_ulcer to x8;

alter table xdr\_123456\_ctlpat2 rename column char\_mild\_liver\_disease to x9;

alter table xdr\_123456\_ctlpat2 rename column char\_dm to x10;

alter table xdr\_123456\_ctlpat2 rename column char\_dmcx to x11;

alter table xdr\_123456\_ctlpat2 rename column char\_paraplegia\_hemiplegia to x12;

alter table xdr\_123456\_ctlpat2 rename column char\_renal\_disease to x13;

alter table xdr\_123456\_ctlpat2 rename column char\_cancer to x14;

alter table xdr\_123456\_ctlpat2 rename column char\_mod\_or\_sev\_liver\_disease to x15;

alter table xdr\_123456\_ctlpat2 rename column char\_metastatic\_carcinoma to x16;

alter table xdr\_123456\_ctlpat2 rename column char\_aids\_hiv to x17;

\--------------------------------------------------------------------------------

\-- Get cosine\_similarity (measure of similarity between case and control)

\-- cosine\_similarity = 0: no match whatsoever

\-- cosine\_similarity < 1: possible match; the higher, the more similar

\-- cosine\_similarity = 1: exact match

\-- cosine\_similarity > 1: should never happen but check to confirm

\--------------------------------------------------------------------------------

drop table xdr\_123456\_ctlpat\_cs2 purge;

create table xdr\_123456\_ctlpat\_cs2 as

select x.\*

,cs\_num / (sqrt(cs\_den\_coh) \* sqrt(cs\_den\_ctl)) as cosine\_similarity

from (select cc.\*

,(coh.x1\*ctl.x1)+(coh.x2\*ctl.x2)+(coh.x3\*ctl.x3)+(coh.x4\*ctl.x4)+(coh.x5\*ctl.x5)

+(coh.x6\*ctl.x6)+(coh.x7\*ctl.x7)+(coh.x8\*ctl.x8)+(coh.x9\*ctl.x9)+(coh.x10\*ctl.x10)

+(coh.x11\*ctl.x11)+(coh.x12\*ctl.x12)+(coh.x13\*ctl.x13)+(coh.x14\*ctl.x14)+(coh.x15\*ctl.x15)

+(coh.x16\*ctl.x16)+(coh.x17\*ctl.x17)

as cs\_num

,(power(coh.x1,2))+(power(coh.x2,2))+(power(coh.x3,2))+(power(coh.x4,2))+(power(coh.x5,2))

+(power(coh.x6,2))+(power(coh.x7,2))+(power(coh.x8,2))+(power(coh.x9,2))+(power(coh.x10,2))

+(power(coh.x11,2))+(power(coh.x12,2))+(power(coh.x13,2))+(power(coh.x14,2))+(power(coh.x15,2))

+(power(coh.x16,2))+(power(coh.x17,2))

as cs\_den\_coh

,(power(ctl.x1,2))+(power(ctl.x2,2))+(power(ctl.x3,2))+(power(ctl.x4,2))+(power(ctl.x5,2))

+(power(ctl.x6,2))+(power(ctl.x7,2))+(power(ctl.x8,2))+(power(ctl.x9,2))+(power(ctl.x10,2))

+(power(ctl.x11,2))+(power(ctl.x12,2))+(power(ctl.x13,2))+(power(ctl.x14,2))+(power(ctl.x15,2))

+(power(ctl.x16,2))+(power(ctl.x17,2))

as cs\_den\_ctl

from xdr\_123456\_cs2ctlpre cc

join xdr\_123456\_cohpat3 coh on cc.coh\_pat\_id = coh.pat\_id --case pts

join xdr\_123456\_ctlpat2 ctl on cc.ctl\_pat\_id = ctl.pat\_id --ctl pts

) x

where cs\_den\_coh > 0 and cs\_den\_ctl > 0

;

alter table xdr\_123456\_ctlpat\_cs2 add constraint xdr\_123456\_ctlpat\_cs2\_pk primary key (coh\_pat\_id, ctl\_pat\_id);

select count(\*), count(distinct coh\_pat\_id), count(distinct ctl\_pat\_id) from xdr\_123456\_ctlpat\_cs2; --2295324 2372 426830

select \* from xdr\_123456\_ctlpat\_cs2;

\--------------------------------------------------------------------------------

\-- Verify cosine\_similarity

\--------------------------------------------------------------------------------

select \* from xdr\_123456\_ctlpat\_cs2;

select cs, count(\*)

from (select case

when cosine\_similarity = 0 then '=0' --no match whatsoever

when cosine\_similarity < 1 then '<1' --possible match; the higher, the more similar

when cosine\_similarity = 1 then '=1' --exact match

when cosine\_similarity > 1 then '>1' --should never happen but checking to confirm

end cs

from xdr\_123456\_ctlpat\_cs2

)

group by cs

;

\--=0 1042910

\--=1 3330

\--<1 1249084

select \* from xdr\_123456\_ctlpat\_cs2 where cosine\_similarity = 0;

select \* from xdr\_123456\_ctlpat\_cs2 where cosine\_similarity = 1;

select \* from xdr\_123456\_ctlpat\_cs2 where cosine\_similarity > 0 and cosine\_similarity < 1;

\--------------------------------------------------------------------------------

\-- Prepare final controls

\-- At this time, determine how many controls you need per case.

\-- If 1 case to 1 control, jump to "Prepare final controls for 1 case to 1 control"

\-- If 1 case to multiple controls, jump to "Prepare final controls for 1 case to many controls"

\--------------------------------------------------------------------------------

/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

Prepare final controls for 1 case to 1 control

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/

\--------------------------------------------------------------------------------

\-- Prepare input to creating control procedure

\-- Start with ranking within each case cohort patient

\--------------------------------------------------------------------------------

drop table xdr\_123456\_ctlpre1 purge; --must include coh\_pat\_id, ctl\_pat\_id, and rank

create table xdr\_123456\_ctlpre1 as

select x.\*

,rank() over (partition by coh\_pat\_id order by coh\_pat\_id, rank\_ctl, rank\_coh) rank

from (select distinct coh\_pat\_id

,rank() over (partition by coh\_pat\_id order by coh\_pat\_id, cosine\_similarity desc, char\_total\_score desc, ctl\_pat\_id) rank\_coh

,rank() over (partition by ctl\_pat\_id order by ctl\_pat\_id, cosine\_similarity desc, char\_total\_score desc, coh\_pat\_id) rank\_ctl

,cosine\_similarity

,char\_total\_score

,ctl\_pat\_id

from xdr\_123456\_ctlpat\_cs2

where cosine\_similarity > 0

) x

;

alter table xdr\_123456\_ctlpre1 add constraint xdr\_123456\_ctlpre1\_pk primary key (coh\_pat\_id, rank, ctl\_pat\_id);

select count(\*), count(distinct coh\_pat\_id), count(distinct ctl\_pat\_id) from xdr\_123456\_ctlpre1; --1252414 2364 334474

select \* from xdr\_123456\_ctlpre1;

select count(\*) --4328=good=all coh and ctl pts are unique

from (select coh\_pat\_id from xdr\_123456\_ctlpre1 where rank\_coh = 1 and rank\_ctl = 1 and rank = 1

union select ctl\_pat\_id from xdr\_123456\_ctlpre1 where rank\_coh = 1 and rank\_ctl = 1 and rank = 1

)

;

\--------------------------------------------------------------------------------

\-- Now save the prioritized ones

\--------------------------------------------------------------------------------

drop table xdr\_123456\_ctlpre2 purge; --must include coh\_pat\_id, ctl\_pat\_id, and rank

create table xdr\_123456\_ctlpre2 as

select x.\*

from xdr\_123456\_ctlpre1 x

where rank\_coh = 1 and rank\_ctl = 1 and rank = 1

order by ctl\_pat\_id, cosine\_similarity desc

;

alter table xdr\_123456\_ctlpre2 add constraint xdr\_123456\_ctlpre2\_pk primary key (coh\_pat\_id, rank, ctl\_pat\_id);

select count(\*), count(distinct coh\_pat\_id), count(distinct ctl\_pat\_id) from xdr\_123456\_ctlpre2; --2164 2164 2164

select \* from xdr\_123456\_ctlpre2;

\--------------------------------------------------------------------------------

\-- Now get the leftovers for the control matching procedure

\-- The prioritized ones are excluded because they are already the top

\-- matches per case

\--------------------------------------------------------------------------------

drop table xdr\_123456\_ctlpre purge;

create table xdr\_123456\_ctlpre as

select x.\*

from xdr\_123456\_ctlpre1 x

left join xdr\_123456\_ctlpre2 sav on x.coh\_pat\_id = sav.coh\_pat\_id

or x.ctl\_pat\_id = sav.ctl\_pat\_id

where sav.coh\_pat\_id is null

;

alter table xdr\_123456\_ctlpre add constraint xdr\_123456\_ctlpre\_pk primary key (coh\_pat\_id, rank, ctl\_pat\_id);

select count(\*), count(distinct coh\_pat\_id), count(distinct ctl\_pat\_id) from xdr\_123456\_ctlpre; --96256 200 68044

select \* from xdr\_123456\_ctlpre;

\--------------------------------------------------------------------------------

\-- Jump to "Run procedure to get unique controls per case"

\--------------------------------------------------------------------------------

/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

Prepare final controls for 1 case to many controls

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/

\--------------------------------------------------------------------------------

\-- Prepare input to creating control procedure

\-- Start with ranking within each case cohort patient

\--------------------------------------------------------------------------------

drop table xdr\_123456\_ctlpre1 purge; --must include coh\_pat\_id, ctl\_pat\_id, and rank

create table xdr\_123456\_ctlpre1 as

select distinct coh\_pat\_id

,rank() over (partition by coh\_pat\_id order by coh\_pat\_id, cosine\_similarity desc, ctl\_pat\_id) rank

,cosine\_similarity

,ctl\_pat\_id

from xdr\_123456\_ctlpat\_cs2

;

alter table xdr\_123456\_ctlpre1 add constraint xdr\_123456\_ctlpre1\_pk primary key (coh\_pat\_id, rank, ctl\_pat\_id);

select count(\*), count(distinct coh\_pat\_id), count(distinct ctl\_pat\_id) from xdr\_123456\_ctlpre1; --164575792 9559 622321

select \* from xdr\_123456\_ctlpre1;

\--------------------------------------------------------------------------------

\-- Get top n controls per case to alleviate the data inputted into the procedure

\--------------------------------------------------------------------------------

drop table xdr\_123456\_ctlpre purge; --must include coh\_pat\_id, ctl\_pat\_id, and rank

create table xdr\_123456\_ctlpre as

select ctl.\*

from xdr\_123456\_ctlpre1 ctl

where ctl.rank <= 5000 --change this at your discretion, increase and rerun if not enough controls reached

;

alter table xdr\_123456\_ctlpre add constraint xdr\_123456\_ctlpre\_pk primary key (coh\_pat\_id, rank, ctl\_pat\_id);

select count(\*), count(distinct coh\_pat\_id), count(distinct ctl\_pat\_id) from xdr\_123456\_ctlpre; --45352259 9559 613073

select \* from xdr\_123456\_ctlpre;

\--------------------------------------------------------------------------------

\-- Jump to "Run procedure to get unique controls per case"

\--------------------------------------------------------------------------------

/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

\-- Run procedure to get unique controls per case

\-- (pi\_project\_id in i2b2.bip\_project.project\_id%type --Must have a cohort-to-control file that exists in ctsi\_research schema, which includes coh\_pat\_id, ctl\_pat\_id, and rank

\-- --named xdr\_pppppp\_ctlpre, where pppppp is the project\_id

\-- --rank must be precoded in the order that PI requests, if any.

\-- -- see I:\\\_BIP\\Consults\\zz Completed\\Hser - Zhu (refresh)\\Hser\_Script\_Control.sql (Elixhauser)

\-- -- or I:\\\_BIP\\Consults\\Bui\_CRRT\\Bui\_CRRT\_Script.sql (Charlson) for examples

\-- ,pi\_max\_number\_matches in integer

\-- )

\-- Alternate method to p\_create\_controls above:

\-- C:\\server\_apps\\eHonestBroker\\Create\_Controls.py

\-- pi\_project\_id = 123456

\-- pi\_max\_number\_matches = n, where n=integer of how many controls are requested per case

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/

exec p\_create\_control(123456,n); --where n=integer of how many controls are requested per case

\--------------------------------------------------------------------------------

\-- Verify procedure results

\--------------------------------------------------------------------------------

select count(\*), count(distinct coh\_pat\_id), count(distinct ctl\_pat\_id) from xdr\_123456\_case2ctl; --200 200 200

select \* from xdr\_123456\_case2ctl;

select rank, count(\*) from xdr\_123456\_case2ctl group by rank order by rank;

\--1 178

\--2 20

\--3 2

select min(cosine\_similarity) from xdr\_123456\_case2ctl; --0.0.3333333333333333333333333333333333333344

select min(cosine\_similarity) from xdr\_123456\_case2ctl where rank = 1; --0.0.4472135954999579392818347337462552470882

select min(cosine\_similarity) from xdr\_123456\_case2ctl where rank = 2; --0.0.3333333333333333333333333333333333333344

select min(cosine\_similarity) from xdr\_123456\_case2ctl where rank = 3; --0.0.816496580927726032732428024901963797324

select \* --no rows found=good=all unique

from xdr\_123456\_case2ctl coh

left join xdr\_123456\_case2ctl ctl on coh.coh\_pat\_id = ctl.ctl\_pat\_id

left join xdr\_123456\_case2ctl cs on coh.ctl\_pat\_id = cs.coh\_pat\_id

where ctl.ctl\_pat\_id is not null

or cs.coh\_pat\_id is not null

;

\--------------------------------------------------------------------------------

\-- Get final controls

\--------------------------------------------------------------------------------

drop table xdr\_123456\_cohctlpat purge;

create table xdr\_123456\_cohctlpat as

select distinct z.\*

,p.pat\_mrn\_id coh\_mrn

,p2.pat\_mrn\_id ctl\_mrn

from

\-- (select x.\* from xdr\_123456\_ctlpre2 x --uncomment if case:control ratio is 1:1, you need this to reinstate the exact matches you saved earlier

\-- union --uncomment if case:control ratio is 1:1, you need this to reinstate the exact matches you saved earlier

\-- select x.\* from --uncomment if case:control ratio is 1:1, you need this to reinstate the exact matches you saved earlier

xdr\_123456\_case2ctl

\-- x --uncomment if case:control ratio is 1:1, you need this to reinstate the exact matches you saved earlier

\-- ) --uncomment if case:control ratio is 1:1, you need this to reinstate the exact matches you saved earlier

z

join i2b2.lz\_clarity\_patient p on z.coh\_pat\_id = p.pat\_id

join i2b2.lz\_clarity\_patient p2 on z.ctl\_pat\_id = p2.pat\_id

;

select count(\*), count(distinct coh\_pat\_id), count(distinct ctl\_pat\_id) from xdr\_123456\_cohctlpat; --2364 2364 2364

select \* from xdr\_123456\_cohctlpat;

\-- Verify unique cases and controls

select count(\*) --4728=good=all coh and ctl pts are unique

from (select coh\_pat\_id from xdr\_123456\_cohctlpat

union select ctl\_pat\_id from xdr\_123456\_cohctlpat

)

;

# Vaccination

(by [Ngan Chau](https://uclabip.atlassian.net/wiki/people/557058:6a532fda-2b06-4abd-8615-2f07f4bd7f9f?ref=confluence) on 1/24/2022)

– the following code retrieves the patients' vaccination records (test)

– this data can be added to the vaccination data retrieved through the ‘procedures’

drop table xdr\_PRINV\_immunizations purge;

create table xdr\_PRINV\_immunizations as

select i1.\*

,i2.name    immunization

,istat.name immunization\_status

,penc.pat\_enc\_csn\_id

,penc.visit\_prov\_id

,penc.department\_id

,cpt.cpt\_code

from xdr\_PRINV\_pat     pat

join immune            i1 on pat.pat\_id=i1.pat\_id

join clarity\_immunzatn i2 on i1.immunzatn\_id=i2.immunzatn\_id

join zc\_immnztn\_status istat on istat.immnztn\_status\_c = i1.immnztn\_status\_c

join pat\_enc penc on i1.imm\_csn = penc.pat\_enc\_csn\_id

left join i2b2.lz\_clarity\_cpt\_code cpt on cpt.pat\_enc\_csn\_id=penc.pat\_enc\_csn\_id

where trunc(immune\_date) <insert date param logic>

and regexp\_like(i2.name,'+(hpv|tdap|meningococcal|flu|Gardasil|mcv4)+','i') – name of vaccines

and istat.name = 'Given'

and regexp\_like(penc.department\_id,'12345|67890');

commit;      

# ICU Care

(by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 3/4/2022)

– the following code flags ICU care based on encoutner name, level of care, or accommodation.

\-------------------------------------------

\-- Identify Intensive Care Unit stays/care

\-------------------------------------------

SELECT adt.\*

from clarity\_adt adt

left join ZC\_LVL\_OF\_CARE zloc on adt.PAT\_LVL\_OF\_CARE\_C = zloc.LEVEL\_OF\_CARE\_C

left join ZC\_ACCOMMODATION za on adt.ACCOMMODATION\_C = za.ACCOMMODATION\_C

left join clarity\_dep dept on adt.department\_id = dept.department\_id

where department\_name like '%ICU%'

OR ( adt.PAT\_LVL\_OF\_CARE\_C IS NOT NULL AND adt.PAT\_LVL\_OF\_CARE\_C = '6') -- ICU

OR adt.ACCOMMODATION\_C IN (25 --ICU

28 --ICU Trauma

46 --ICU Pediatric

);

# SmartData Elements

(by ​[Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 7/30/2022)

This link includes the list of [SmartData Elements in EPIC](https://galaxy.epic.com/Search/GetFile?url=1!68!100!1005313&rank=5&queryid=96416879&docid=61648) and the actual file is under I:\\Bell\_BIP\\xDR\\Lists and Codes\\New Standard SmartData Elements.xlsx.

The caveat is that this list is the generic one from EPIC which means that customized smartdata elements created at UCLA would not be included here.

The following code is just a very first draft to pull elements but further enhancements might be needed to optimize the data pull.

\-------------------------------------------

\-- Extract SmartData Elements information

\-------------------------------------------

select distinct coh.pat\_id

,sed.ELEMENT\_ID

\-- once we have the table referenced above loaded into our schema, we can simply left join to it and get the name of the element

\-- based on the element ID, the example below was hardcoded becase our team was involved in the creation of the form

\-- and were told the element IDs behind each question

\-- ,case

\-- when sed.ELEMENT\_ID = 'EPIC#31000090752' then 'AD < 3 YEARS OLD IN CHART'

\-- when sed.ELEMENT\_ID = 'UCLATEAM#330' then 'POLST < 3 YEARS OLD IN CHART'

\-- when sed.ELEMENT\_ID = 'UCLATEAM#331' then 'AD < 3 YEARS OLD AT HOME COMPLETED'

\-- when sed.ELEMENT\_ID = 'UCLATEAM#332' then 'AD > 3 YEARS OLD ON FILE STILL VALID'

\-- when sed.ELEMENT\_ID = 'UCLATEAM#333' then 'WILLING TO USE PREPARE FOR YOUR CARE WEBSITE'

\-- when sed.ELEMENT\_ID = 'UCLATEAM#336' then 'INTRODUCING AD COMPLETION WITH PATIENT'

\-- when sed.ELEMENT\_ID = 'UCLATEAM#338' then 'IS PATIENT WILLING TO USE PREPARE FOR YOUR CARE WEBSITE'

\-- when sed.ELEMENT\_ID = 'UCLATEAM#339' then 'ACP OUTREACH TIMESTAMP'

\-- when sed.ELEMENT\_ID = 'UCLATEAM#341' then 'PATIENT WILL COMPLETE AD AND SUBMIT VIA:'

\-- when sed.ELEMENT\_ID = 'UCLATEAM#342' then 'PATIENT WILL COMPLETE AD AND SUBMIT VIA OTHER COMMENT:'

\-- when sed.ELEMENT\_ID = 'UCLATEAM#344' then 'AD < 3 YEARS OLD AT HOME COMPLETED OTHER COMMENT'

\-- when sed.ELEMENT\_ID = 'UCLATEAM#345' then 'PATIENT NOT READY TO PROCEED WITH AD. CCC TO FOLLOW UP ON:'

\-- when sed.ELEMENT\_ID = 'UCLATEAM#346' then 'POLST'

\-- when sed.ELEMENT\_ID = 'UCLATEAM#347' then 'PATIENT REFUSE'

\-- when sed.ELEMENT\_ID = 'UCLATEAM#348' then 'OUTREACH COMMENTS'

\-- else 'OTHER'

\-- end smart\_element\_name

,sed.CUR\_SOURCE\_LQF\_ID

,sed.CUR\_VALUE\_DATETIME

,sed.UPDATE\_DATE

,sed.REC\_ARCHIVED\_YN

,sed.CUR\_VAL\_UTC\_DTTM

,sed.HLV\_ID

,sev.line

,sev.smrtdta\_elem\_value

,CUR\_SOURCE\_LQF\_ID

\-- ,sysdate as creation\_date

\-- if you need to pull te user who filled out the form

\-- ,sed.CUR\_VALUE\_USER\_ID as user\_id

\-- ,emp.name as user\_name

from i2b2.lz\_clarity\_patient coh

join clarity.smrtdta\_elem\_data sed on coh.pat\_id = sed.PAT\_LINK\_ID

join clarity.SMRTDTA\_ELEM\_VALUE sev on sed.hlv\_id = sev.hlv\_id

\--left join clarity.clarity\_emp emp on sed.CUR\_VALUE\_USER\_ID = emp.USER\_ID

where

\-- if we know the form ID

\--sed.CUR\_SOURCE\_LQF\_ID = 992' --ACP outreach

sed.ELEMENT\_ID = 'EPIC#31000090752'

# Birth Control / Contraception / Sexually Active

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 8/10/2022)

– the following gets birth control/contraception and sexually active data for patients

select distinct coh.pat\_id

,xsa.name sexually\_active

,s.contact\_date

,max(s.contact\_date) over (partition by coh.pat\_id) latest\_contact\_date

,s.condom\_yn

,s.pill\_yn

,s.diaphragm\_yn

,s.iud\_yn

,s.surgical\_yn

,s.spermicide\_yn

,s.implant\_yn

,s.rhythm\_yn

,s.injection\_yn

,s.sponge\_yn

,s.inserts\_yn

,s.abstinence\_yn

from xdr\_123456\_cohpat coh

join social\_hx s on coh.pat\_id = s.pat\_id

left join zc\_sexually\_active xsa on s.sexually\_active\_c = xsa.sexually\_active\_c

\-- where xsa.name = 'Yes'

;

# PHI disclosure HHS Guidelines

(by [Fernando Sanz-Vidorreta](https://uclabip.atlassian.net/wiki/people/557058:05226a48-f9fe-405f-8bf2-736ba4603c19?ref=confluence) on 10/10/2022)

IRB can supersede these guidelines since every case is evaluated separately but when an IRB and/or Compliance Approval do not apply (ex counts) please use this information.

[https://www.hhs.gov/hipaa/for-professionals/privacy/special-topics/de-identification/index.html#zip](https://www.hhs.gov/hipaa/for-professionals/privacy/special-topics/de-identification/index.html#zip)

# Provider Email

(by [Cenan Pirani](https://uclabip.atlassian.net/wiki/people/5f5922bcff2fe2006fb700d8?ref=confluence) on 10/20/2022)

### Get provider emails using the ‘prov\_id’:

select distinct emp.prov\_id

,lower(substr(vu.ad\_username,4)) || '@mednet.ucla.edu' as prov\_email

from cohort\_enc enc

join clarity\_emp emp on enc.visit\_prov\_id = emp.prov\_id

join v\_cube\_d\_user vu on emp.user\_id = vu.user\_id

;

# ICD-10-CM Updates to Lookup Table

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 10/24/2022)

### Update ICD-10 dx lookup table:

Steps:

1.  Download ICD-10 file from [cms.gov](http://cms.gov) and search for icd-10.
2.  Run python program below to create a new file (ICD-10 CM codes in this file typically do not contain the period). Note that this is required for ICD-10 CM diagnosis codes, and not ICD-10 PCS procedure codes. ICD-10 PCS procedure codes can simply be downloaded from [cms.gov](http://cms.gov) and imported into the icd10\* table as one column (COLUMN1) to be loaded into the I2B2.LZ\_DX\_PX\_LOOKUP table.
3.  Run sql script below to insert codes that do not exist in I2B2.LZ\_DX\_PX\_LOOKUP table.

"""

@author: ttacorda

"""

import os

filepath = ("I:\\\\\_Theona\\\\icd10\_dx\_updates\\\\2023\_Updates\\\\2023 Code Descriptions in Tabular Order\\\\")

filein = str(filepath + 'icd10cm\_codes\_2023.txt')

fileout = str(filepath + 'icd10cm\_codes\_2023.txt\_new.csv')

\# Delete the formatted file first, if one exists

if os.path.exists(fileout):

os.remove(fileout)

print('starting...')

writeFile = open(fileout, 'w', encoding='utf8')

i=0

writeFile.write('"icd\_code","icd\_description"\\n')

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

\# After this program is run, import to i2b2.icd10\_yyyy, where yyyy is the icd-10 import year.

readFile.close()

writeFile.close()

print('ending...')

\-----------------------------------------------------------------------------------------------------

\-- Housekeeping

\-----------------------------------------------------------------------------------------------------

\-- Import from cms.gov (CM or PCS ICD-10 codes)

drop table icd10\_2023 purge;

select count(\*) from i2b2.icd10\_2023; --73639

select \* from i2b2.icd10\_2023;

select \* from i2b2.lz\_dx\_px\_lookup;

\-----------------------------------------------------------------------------------------------------

\-- ICD-10 CM diagnosis code refresh

\-----------------------------------------------------------------------------------------------------

insert into i2b2.lz\_dx\_px\_lookup (code, code\_type, icd\_type, icd\_desc)

select trim(icd\_code) code

,'DX' code\_type

,10 icd\_type

,trim(icd\_description) icd\_desc

from i2b2.icd10\_2023 x

left join i2b2.lz\_dx\_px\_lookup dl on trim(x.icd\_code) = dl.code

and dl.code\_type = 'DX'

and dl.icd\_type = 10

where dl.code is null

;

commit; --1,343 rows inserted.

\-----------------------------------------------------------------------------------------------------

\-- ICD-10 PCS procedure code refresh

\-----------------------------------------------------------------------------------------------------

insert into i2b2.lz\_dx\_px\_lookup (code, code\_type, icd\_type, icd\_desc)

select substr(column1,1,7) code

,'PX' code\_type

,10 icd\_type

,trim(substr(column1,8)) icd\_desc

from i2b2.icd10\_2023 x

left join i2b2.lz\_dx\_px\_lookup dl on substr(x.column1,1,7) = dl.code

and dl.code\_type = 'PX'

and dl.icd\_type = 10

where dl.code is null

;

commit; --7,772 rows inserted.

\-----------------------------------------------------------------------------------------------------

\-- Verification and cleanup

\-----------------------------------------------------------------------------------------------------

drop table icd10\_2023 purge;

select code\_type, count(\*) from i2b2.lz\_dx\_px\_lookup where icd\_type = 10 group by code\_type order by code\_type;

select \* from i2b2.lz\_dx\_px\_lookup where icd\_desc is null; --no rows found=good

# Escape special characters in large texts

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 12/12/2022)

Steps:

1.  Create text tables (e.g. notes, pathology results, imaging narratives/impressions) named xdr\_123456\_entity\_cd, where 123456 is the project\_id and entity\_cd is the element (e.g. NOTE, PATHR, IMGN/IMGI, respectively).
2.  Run python program below to create a new text files. Change project id and entity codes accordingly. This program is also located in I:\_BIP\\xDR\\Python\\escape\_text\_from\_oracle\_to\_redcapcsv.py
3.  Provide new files to DOMSTATS or load directly to REDCap, whichever is appropriate for your project.

\# -\*- coding: utf-8 -\*-

"""

Created on Wed Apr 7 05:58:27 2021

@author: TheonaT1

"""

\# import commands

import os

import cx\_Oracle as Ora

\# import csv

filepath = ("E:\\\\Projects\\\\123456\\\\Data\\\\")

print('starting...')

batch\_size = 100000

\# Format date

CLdb = Ora.connect('ctsi\_research/S5archR3\_CTsi@clarityq')

cur = CLdb.cursor()

sql = 'alter session set nls\_date\_format = \\'MM/DD/YYYY HH24:MI\\'';

cur.execute(sql)

\# -----------------------------------------------------------------------------

\# Provider\_Notes

\# -----------------------------------------------------------------------------

print('starting Provider\_Notes...')

fileout = str(filepath + 'Provider\_Notes\_REDCap\_Final.txt')

\# Delete output file if it exists

if os.path.exists(fileout):

os.remove(fileout)

writeFile = open(fileout, 'w', encoding='utf8', errors="ignore")

\# Write header

v\_col = 'ip\_patient\_id,ip\_enc\_id,ip\_note\_id,note\_type,create\_datetime,create\_by,line\_number,note\_text,contact\_date'

writeFile.write(v\_col + '\\n')

\# Get and Write rows

sql = 'select \\'"\\' || study\_id ' \\

'|| \\'"\\' || \\'|\\' || \\'"\\' || study\_csn ' \\

'|| \\'"\\' || \\'|\\' || \\'"\\' || study\_note\_id ' \\

'|| \\'"\\' || \\'|\\' || \\'"\\' || note\_type ' \\

'|| \\'"\\' || \\'|\\' || \\'"\\' || create\_datetime ' \\

'|| \\'"\\' || \\'|\\' || \\'"\\' || create\_by ' \\

'|| \\'"\\' || \\'|\\' || \\'"\\' || line\_number ' \\

'|| \\'"\\' || \\'|\\' || \\'"\\' || note\_text ' \\

'|| \\'"\\' || \\'|\\' || \\'"\\' || contact\_date ' \\

'|| \\'"\\' export ' \\

'from (select distinct study\_id, study\_csn, study\_note\_id, note\_type, create\_datetime' \\

', create\_by, line\_number, note\_text, contact\_date ' \\

'from xdr\_123456\_note ' \\

'order by study\_id, study\_note\_id, line\_number)';

\# print(sql)

i=0

try:

with CLdb.cursor() as cur:

cur.execute(sql)

while True:

rows = cur.fetchmany(batch\_size)

if not rows:

break

for row in rows:

v\_col = row\[0\]

v\_col = v\_col.replace('"', '', 1) # reset first "

v\_col = v\_col\[:-1\] # remove last "

v\_col = v\_col.replace('"|"', '|||xxxyyz') # remove double-quotes and change delimiter

v\_col = v\_col.replace('\\\\', '\\\\\\\\') # escape backslashes

v\_col = v\_col.replace('\\"', '""') # escape double-quotes

v\_col = v\_col.replace('|||xxxyyz', '","') # reset all delimiters back to commas

v\_col = '"' + v\_col # reinstate first "

v\_col = v\_col + '"' # reinstate last "

writeFile.write(v\_col + '\\n')

i += 1

except CLdb.Error as error:

print(error)

writeFile.close()

print('ending Provider\_Notes...')

\# -----------------------------------------------------------------------------

\# Pathology\_Results

\# -----------------------------------------------------------------------------

print('starting Pathology\_Results...')

fileout = str(filepath + 'Pathology\_Results\_REDCap\_Final3.txt')

\# Delete output file if it exists

if os.path.exists(fileout):

os.remove(fileout)

writeFile = open(fileout, 'w', errors="ignore")

\# Write header

v\_col = 'ip\_patient\_id,ip\_order\_proc\_id,component\_id,component\_name,sort\_key,result\_txt'

writeFile.write(v\_col + '\\n')

\# Get and Write rows

sql = 'select \\'"\\' || study\_id ' \\

'|| \\'"\\' || \\'|\\' || \\'"\\' || study\_order\_proc\_id ' \\

'|| \\'"\\' || \\'|\\' || \\'"\\' || component\_id ' \\

'|| \\'"\\' || \\'|\\' || \\'"\\' || component\_name ' \\

'|| \\'"\\' || \\'|\\' || \\'"\\' || sort\_key ' \\

'|| \\'"\\' || \\'|\\' || \\'"\\' || result\_txt ' \\

'|| \\'"\\' export ' \\

'from (select distinct study\_id, study\_order\_proc\_id, component\_id, component\_name' \\

', sort\_key, result\_txt ' \\

'from xdr\_123456\_pathr ' \\

'order by study\_id, study\_order\_proc\_id, component\_id, sort\_key)';

\# print(sql)

i=0

try:

with CLdb.cursor() as cur:

cur.execute(sql)

while True:

rows = cur.fetchmany(batch\_size)

if not rows:

break

for row in rows:

v\_col = row\[0\]

v\_col = v\_col.replace('"', '', 1) # reset first "

v\_col = v\_col\[:-1\] # remove last "

v\_col = v\_col.replace('"|"', '|||xxxyyz') # remove double-quotes and change delimiter

v\_col = v\_col.replace('\\\\', '\\\\\\\\') # escape backslashes

v\_col = v\_col.replace('\\"', '""') # escape double-quotes

v\_col = v\_col.replace('|||xxxyyz', '","') # reset all delimiters back to commas

v\_col = '"' + v\_col # reinstate first "

v\_col = v\_col + '"' # reinstate last "

writeFile.write(v\_col + '\\n')

i += 1

except CLdb.Error as error:

print(error)

writeFile.close()

print('ending Pathology\_Results...')

\# -----------------------------------------------------------------------------

\# Imaging\_Impressions

\# -----------------------------------------------------------------------------

print('starting Imaging\_Impressions...')

fileout = str(filepath + 'Imaging\_Impressions\_REDCap\_Final.txt')

\# Delete output file if it exists

if os.path.exists(fileout):

os.remove(fileout)

writeFile = open(fileout, 'w', errors="ignore")

\# Write header

v\_col = 'ip\_patient\_id,ip\_order\_proc\_id,line,impression'

writeFile.write(v\_col + '\\n')

\# Get and Write rows

sql = 'select \\'"\\' || study\_id ' \\

'|| \\'"\\' || \\'|\\' || \\'"\\' || study\_order\_proc\_id ' \\

'|| \\'"\\' || \\'|\\' || \\'"\\' || line ' \\

'|| \\'"\\' || \\'|\\' || \\'"\\' || impression ' \\

'|| \\'"\\' export ' \\

'from (select distinct study\_id, study\_order\_proc\_id, line, impression ' \\

'from xdr\_123456\_imgi ' \\

'order by study\_id, study\_order\_proc\_id, line)';

\# print(sql)

i=0

try:

with CLdb.cursor() as cur:

cur.execute(sql)

while True:

rows = cur.fetchmany(batch\_size)

if not rows:

break

for row in rows:

v\_col = row\[0\]

v\_col = v\_col.replace('"', '', 1) # reset first "

v\_col = v\_col\[:-1\] # remove last "

v\_col = v\_col.replace('"|"', '|||xxxyyz') # remove double-quotes and change delimiter

v\_col = v\_col.replace('\\\\', '\\\\\\\\') # escape backslashes

v\_col = v\_col.replace('\\"', '""') # escape double-quotes

v\_col = v\_col.replace('|||xxxyyz', '","') # reset all delimiters back to commas

v\_col = '"' + v\_col # reinstate first "

v\_col = v\_col + '"' # reinstate last "

writeFile.write(v\_col + '\\n')

i += 1

except CLdb.Error as error:

print(error)

writeFile.close()

print('ending Imaging\_Impressions...')

\# -----------------------------------------------------------------------------

\# Imaging\_Narratives

\# -----------------------------------------------------------------------------

print('starting Imaging Narratives...')

fileout = str(filepath + 'Imaging\_Narratives\_REDCap\_Final.txt')

\# Delete output file if it exists

if os.path.exists(fileout):

os.remove(fileout)

writeFile = open(fileout, 'w', errors="ignore")

\# Write header

v\_col = 'ip\_patient\_id,ip\_order\_proc\_id,line,narrative'

writeFile.write(v\_col + '\\n')

\# Get and Write rows

sql = 'select \\'"\\' || study\_id ' \\

'|| \\'"\\' || \\'|\\' || \\'"\\' || study\_order\_proc\_id ' \\

'|| \\'"\\' || \\'|\\' || \\'"\\' || line ' \\

'|| \\'"\\' || \\'|\\' || \\'"\\' || narrative ' \\

'|| \\'"\\' export ' \\

'from (select distinct study\_id, study\_order\_proc\_id, line, narrative ' \\

'from xdr\_123456\_imgn ' \\

'order by study\_id, study\_order\_proc\_id, line)';

\# print(sql)

i=0

try:

with CLdb.cursor() as cur:

cur.execute(sql)

while True:

rows = cur.fetchmany(batch\_size)

if not rows:

break

for row in rows:

v\_col = row\[0\]

v\_col = v\_col.replace('"', '', 1) # reset first "

v\_col = v\_col\[:-1\] # remove last "

v\_col = v\_col.replace('"|"', '|||xxxyyz') # remove double-quotes and change delimiter

v\_col = v\_col.replace('\\\\', '\\\\\\\\') # escape backslashes

v\_col = v\_col.replace('\\"', '""') # escape double-quotes

v\_col = v\_col.replace('|||xxxyyz', '","') # reset all delimiters back to commas

v\_col = '"' + v\_col # reinstate first "

v\_col = v\_col + '"' # reinstate last "

writeFile.write(v\_col + '\\n')

i += 1

except CLdb.Error as error:

print(error)

writeFile.close()

print('ending Imaging\_Narratives...')

print('ending...')

# Splitting table data into smaller files

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 12/14/2022)

Steps:

1.  Create table named xdr\_123456\_entity\_cd, where 123456 is the project\_id and entity\_cd is the element (e.g. NOTE, PATHR, IMGN/IMGI, respectively). Need to create RN column for rownum (example below).
2.  Run python program below to create a multiple files. Change project id and entity codes accordingly. This program is also located in I:\_BIP\\xDR\\Python\\split\_oracle\_tables\_to\_mult\_files\_4redcap.py. Note that this program also takes care of escaping special characters in large text files.

drop table xdr\_123456\_imgn\_rc purge;

create table xdr\_123456\_imgn\_rc as

select rownum rn

,x.\*

from (select distinct coh.study\_id\_rc "study\_id"

,'imaging\_narratives' "redcap\_repeat\_instrument"

,row\_number() over (partition by x.study\_id order by x.study\_id, x.study\_order\_proc\_id, x.line) as "redcap\_repeat\_instance"

,x.study\_order\_proc\_id "ip\_order\_proc\_id\_narrative"

,x.line "narrative\_line"

,x.narrative "narrative"

,0 "imaging\_narratives\_complete"

from xdr\_123456\_DEMO\_rc coh

join xdr\_123456\_IMGN x on coh.study\_id = x.study\_id

order by coh.study\_id\_rc, x.study\_order\_proc\_id, "redcap\_repeat\_instance"

) x

;

import os

import cx\_Oracle as Ora

print('starting...')

#------------------------------------------------------------------------------

\# Start customizations!!!

\# Pre-requisite: Create xdr\_123456\_entity\_cd\_rc,

\# where 123456=project\_id

\# and entity\_cd = entity\_cd (e.g. IMGN=imaging narratives)

#------------------------------------------------------------------------------

filepath = ("I:\\\\\_BIP\\\\Consults\\\\")

batch\_size = 100000

v\_limit = 14000

v\_projid = '123456'

v\_entcd = 'IMGN'

v\_file = 'Imaging\_Narratives\_'

v\_hdr = 'study\_id,redcap\_repeat\_instrument,redcap\_repeat\_instance,ip\_order\_proc\_id\_narrative,narrative\_line,narrative,imaging\_narratives\_complete'

sql = 'select rn, \\'"\\' || "study\_id" ' \\

'|| \\'"\\' || \\'|\\' || \\'"\\' || "redcap\_repeat\_instrument" ' \\

'|| \\'"\\' || \\'|\\' || \\'"\\' || "redcap\_repeat\_instance" ' \\

'|| \\'"\\' || \\'|\\' || \\'"\\' || "ip\_order\_proc\_id\_narrative" ' \\

'|| \\'"\\' || \\'|\\' || \\'"\\' || "narrative\_line" ' \\

'|| \\'"\\' || \\'|\\' || \\'"\\' || "narrative" ' \\

'|| \\'"\\' || \\'|\\' || \\'"\\' || "imaging\_narratives\_complete" ' \\

'|| \\'"\\' export ' \\

'from (select distinct rn,"study\_id","redcap\_repeat\_instrument","redcap\_repeat\_instance"' \\

',"ip\_order\_proc\_id\_narrative","narrative\_line","narrative","imaging\_narratives\_complete"' \\

'from xdr\_' + v\_projid + '\_' + v\_entcd + '\_rc ' \\

'order by "study\_id", "redcap\_repeat\_instance")';

#print(sql)

#------------------------------------------------------------------------------

\# End customizations!!!

#------------------------------------------------------------------------------

\# Format date

CLdb = Ora.connect('ctsi\_research/S5archR3\_CTsi@clarityq')

cur = CLdb.cursor()

presql = 'alter session set nls\_date\_format = \\'MM/DD/YYYY HH24:MI\\'';

cur.execute(presql)

presql = 'select max(rn) into :a from xdr\_' + v\_projid + '\_' + v\_entcd + '\_rc';

cur.execute(presql)

v\_rows = cur.fetchone()

v\_max\_rows = v\_rows\[0\]

print(str('v\_max\_rows=' + str(v\_max\_rows)))

v\_ctr\_min = 1

v\_ctr\_max = v\_limit

v\_ctr\_file = 1

print(str('v\_ctr\_min=' + str(v\_ctr\_min) + ', v\_ctr\_max=' + str(v\_ctr\_max)))

fileout = str(filepath + v\_file + str(v\_ctr\_file) + '.csv')

if os.path.exists(fileout):

os.remove(fileout)

writeFile = open(fileout, 'w', encoding='utf8', errors="ignore")

writeFile.write(v\_hdr + '\\n')

try:

with CLdb.cursor() as cur:

cur.execute(sql)

while True:

rows = cur.fetchmany(batch\_size)

if not rows:

break

for row in rows:

v\_col = row\[1\]

v\_col = v\_col.replace('"', '', 1) # reset first "

v\_col = v\_col\[:-1\] # remove last "

v\_col = v\_col.replace('"|"', '|||xxxyyz') # remove double-quotes and change delimiter

v\_col = v\_col.replace('\\\\', '\\\\\\\\') # escape backslashes

v\_col = v\_col.replace('\\"', '""') # escape double-quotes

v\_col = v\_col.replace('|||xxxyyz', '","') # reset all delimiters back to commas

v\_col = '"' + v\_col # reinstate first "

v\_col = v\_col + '"' # reinstate last "

if (v\_ctr\_min <= row\[0\] <= v\_ctr\_max):

writeFile.write(v\_col + '\\n')

else:

writeFile.close()

v\_ctr\_file = v\_ctr\_file + 1

fileout = str(filepath + v\_file + str(v\_ctr\_file) + '.csv')

if os.path.exists(fileout):

os.remove(fileout)

writeFile = open(fileout, 'w', encoding='utf8', errors="ignore")

writeFile.write(v\_hdr + '\\n')

writeFile.write(v\_col + '\\n')

v\_ctr\_min = v\_ctr\_max + 1

v\_ctr\_max = v\_ctr\_max + v\_limit

print(str('v\_ctr\_min=' + str(v\_ctr\_min) + ', v\_ctr\_max=' + str(v\_ctr\_max)))

except CLdb.Error as error:

print(error)

writeFile.close()

print('ending...')

# Patient Set from I2B2 Query

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 2/6/2023)

– the following gets a patient set from an I2B2 query. Change :i2b2\_query\_name to your i2b2 query.

SELECT DISTINCT pat.PATIENT\_NUM

                    FROM QT\_QUERY\_MASTER qm

                    JOIN QT\_QUERY\_INSTANCE qi

                        ON qm.QUERY\_MASTER\_ID = qi.QUERY\_MASTER\_ID

                    JOIN QT\_QUERY\_RESULT\_INSTANCE       qr

                        ON qi.QUERY\_INSTANCE\_ID = qr.QUERY\_INSTANCE\_ID

                    left JOIN QT\_PATIENT\_SET\_COLLECTION pat

                        ON qr.RESULT\_INSTANCE\_ID = pat.RESULT\_INSTANCE\_ID

                    WHERE

                        qr.RESULT\_TYPE\_ID = 1

                        AND qm.name = :i2b2\_query\_name

;

# Patient Set from User Patient List in Care Connect

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 9/18/2024)

– the following gets a patient set from an Care Connect based on a user’s Patient List.

select pl.list\_id

,pli.list\_description

,count(distinct pl.pat\_id)

from pat\_list pl

join pat\_list\_info pli on pl.list\_id = pli.list\_id

where pli.list\_creator\_id = 'uid' --replace uid with investigator user\_id, then ask which is the requested patient list

group by pl.list\_id

,pli.list\_description

order by pli.list\_description

;

# PROMIS Queries

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 3/27/2023)

– the following gets information regarding PROMIS questionnaires

\--------------------------------------------------------------------------------

\-- Get questionnaires (example-change accordingly per your requirements)

\-- • CareConnect Questionnaire Name and ID - EDMONTON SYMPTOM ASSESSMENT SYSTEM \[14010000014\]

\-- • CareConnect Questionnaire Name and ID - PROMIS Adult Short Form v 1.0 Physical Function \[1400001701\]

\-- • CareConnect Questionnaire Name and ID - PROMIS Adult Short Form v 1.0 Anxiety \[1400001703\]

\-- • CareConnect Questionnaire Name and ID - PROMIS Adult Short Form v 1.0 Fatigue \[1400001705\]

\-- • CareConnect Questionnaire Name and ID - PROMIS Adult Short Form v 1.0 Sleep Disturbance \[1400001706\]

\-- • CareConnect Questionnaire Name and ID - PROMIS Adult Short Form v 1.0 Pain Interference \[1400001708\]

\-- • CareConnect Questionnaire Name and ID - PROMIS Adult Short Form v 1.0 Pain Intensity \[1400001709\]

\-- • CareConnect Questionnaire Name and ID - PROMIS Adult Short Form v 1.0 Global Health \[1400001710\]

\-- • CareConnect Questionnaire MYC UCLA Head and Neck OTV Survey \[140000051\]

\-- • CareConnect Questionnaire MYC UCLA Head and Neck PRO-CTCAE \[140000007\]

\-- • CareConnect Questionnaire MYC UCLA Head and Neck Survey-CON/FU \[140000010\]

\--------------------------------------------------------------------------------

select distinct pefa.pat\_id

,pefa.pat\_enc\_csn\_id

,pefa.qf\_lqf\_id as form\_id

,pefa.qf\_hqa\_id as answer\_id

,qf.form\_name

,qf.pat\_frndly\_name

,cqa.quest\_id

,clqo.question

,cqa.line as ans\_line

,quest\_answer

,question\_instant as answer\_time

from pat\_enc\_form\_ans pefa

join cl\_qform qf on pefa.qf\_lqf\_id = qf.form\_id

join cl\_qanswer\_qa cqa on pefa.qf\_hqa\_id = cqa.answer\_id

join cl\_qquest cqq on cqa.quest\_id = cqq.quest\_id -- question name

join cl\_qquest\_ovtm clqo on cqa.quest\_id = clqo.quest\_id -- question

where regexp\_like(qf.form\_name,'+(PROMIS)+','i')

or regexp\_like(qf.pat\_frndly\_name,'+(PROMIS)+','i')

or regexp\_like(clqo.question,'+(PROMIS)+','i')

or pefa.qf\_lqf\_id in (14010000014

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

order by pefa.pat\_id, pefa.pat\_enc\_csn\_id, pefa.qf\_lqf\_id, cqa.line

;

\--------------------------------------------------------------------------------

\-- Get smartforms

\--------------------------------------------------------------------------------

select distinct vo.pat\_id

,vo.qnr\_id as form\_id

,qf.form\_name

,qf.pat\_frndly\_name

,cqa.quest\_id

,clqo.question

,vo.qnr\_ans\_id as answer\_id

,cqa.line as ans\_line

,cqa.quest\_answer

,cqa.question\_instant as answer\_time

,vo.srs\_id series\_id

,si.series\_name

from v\_pat\_reported\_outcomes vo

join cl\_qanswer\_qa cqa on vo.ques\_id = cqa.quest\_id

and vo.qnr\_ans\_id = cqa.answer\_id

and vo.ques\_ans\_line = cqa.line

join cl\_qform qf on vo.qnr\_id = qf.form\_id

join cl\_qquest cqq on vo.ques\_id = cqq.quest\_id -- question name

join cl\_qquest\_ovtm clqo on vo.ques\_id = clqo.quest\_id -- question

join srs\_info si on vo.srs\_id = si.series\_id

where regexp\_like(si.series\_name,'+(PROMIS)+','i')

or regexp\_like(qf.form\_name,'+(PROMIS)+','i')

or regexp\_like(qf.pat\_frndly\_name,'+(PROMIS)+','i')

or regexp\_like(clqo.question,'+(PROMIS)+','i')

;

\--------------------------------------------------------------------------------

\-- Other potential findings

\--------------------------------------------------------------------------------

select distinct pefa.pat\_enc\_csn\_id

,pefa.pat\_id

,pefa.contact\_date

,pefa.qf\_lqf\_id

,pefa.qf\_hqa\_id

,pefa.qf\_status\_c

,qf.form\_name

,cqa.answer\_id

,cqa.line

,cqa.quest\_id

,cqa.quest\_dat

,cqa.quest\_answer

,cqa.question\_instant

,cqa.float\_answer

,cqa.numeric\_answer

,cqa.varchar\_answer

,cqq.quest\_name

,clqo.question

,pefa.qf\_cosign\_emp\_id

,case when coh.pat\_id is null then 1 else 0 end pat\_in\_lz\_tbl\_yn

from pat\_enc\_form\_ans pefa

left join i2b2.lz\_clarity\_patient coh on pefa.pat\_id = coh.pat\_id

left join cl\_qform qf on pefa.qf\_lqf\_id = qf.form\_id

left join cl\_qanswer\_qa cqa on pefa.qf\_hqa\_id = cqa.answer\_id

left join cl\_qquest cqq on cqa.quest\_id = cqq.quest\_id -- question name

left join cl\_qquest\_ovtm clqo on cqa.quest\_id = clqo.quest\_id -- question

where pefa.qf\_lqf\_id in (14010000014 --change according to your requirements

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

1.  Import results to ctsi\_research.xdr\_xxbill\_all, where xx are your initials. TT is used as the example below.
    1.  Ensure data size is large enough to accept all values (e.g. issue=varchar(2000), worklog=varchar(4000)).
    2.  Rename DATE to dt.
2.  Run the following script.

\--------------------------------------------------------------------------------

\-- Steps:

\-- 1. Run Jira TT\_Billing\_All report for all - DELETE first COLUMN

\-- 2. Import #1 results to xdr\_ttbill\_all

\--------------------------------------------------------------------------------

drop table xdr\_ttbill\_all purge;

select \* from xdr\_ttbill\_all order by key desc;

\--

drop table xdr\_ttbill\_fin purge;

create table xdr\_ttbill\_fin as

select distinct 'q' tp

,key jira\_key

,issue jira\_title

,sum(logged) over (partition by key) logged

from xdr\_ttbill\_all c

where nvl(key,'TOTAL') like 'XDR%'

and worklog is not null

and do\_not\_bill\_these\_hours = 'false'

and to\_date(substr(dt,1,9),'dd/mon/yy') between to\_date('04/01/2023','mm/dd/yyyy') and sysdate --change to reflect current billable quarter

union

select distinct 'a' tp

,key jira\_key

,issue jira\_title

,sum(logged) over (partition by key) logged

from xdr\_ttbill\_all c

where nvl(key,'TOTAL') like 'XDR%'

and worklog is not null

and do\_not\_bill\_these\_hours = 'false'

order by jira\_key

;

select distinct c.jira\_key

,c.jira\_title

,c.logged currqtr\_logged

,a.logged all\_logged

from xdr\_ttbill\_fin c

join xdr\_ttbill\_fin a on c.jira\_key = a.jira\_key and a.tp = 'a'

where c.tp = 'q'

and a.logged > 20

order by c.jira\_key

;

# Cancer Registrar Datamart METS

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 6/27/2023)

– The following gets determines metastatic cancer (Use I:\\\_BIP\\xDR\\Cancer registry integration\\Documentation\_asof\_12222022\\Code\_Library.sql for preliminary tables)

\-- Check for mets

\--Pre-2018 (#Temp123456CSTG1)

select \*

from cancerregistrardm.rpt.cr\_codelookupdim zcmdlu

where zcmdlu.fieldname in ('CS Mets at Dx-Bone','CS Mets at Dx-Brain','CS Mets at Dx-Liver','CS Mets at Dx-Lung') --If value = '1', it's METS

order by fieldname, lookupcode

;

select distinct

CS\_MetsAtDiagnosis --Where can I find meaningful values for these? --ASSUMING '10' and '70' as METS

,CS\_MetsEval --Where can I find meaningful values for these? --ASSUMING ='1' as METS

from #Temp123456CSTG1

where CS\_MetsAtDiagnosis is not null

and CS\_MetsEval is not null

\--order by ip\_patient\_id

;

\--Post-2018 (#Temp123456CSTG2)

select \* from cancerregistrardm.rpt.cr\_codelookupdim zcmdlu

where zcmdlu.fieldname in ('Mets at DX, Bone','Mets at DX, Brain','Mets at DX, Liver','Mets at DX, Lung','Mets at DX, Other'

,'Summary Stage 2018','EOD Primary Tumor','EOD Regional Nodes','EOD Mets')

order by fieldname, lookupcode

;

\--METS assumptions:

\--mets at dx = '1'

\--eod mets between '10' and '70'

\--eod primary tumor between '050' and '750'

\--eod regional nodes between '050' and '750'

\--summary stage 2018 between '1' and '8' --'8'=BENIGN OR BORDERLINE BRAIN???

# Discharge Location

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 2/27/2024)

– The following determines to what general location a patient was discharged.

drop table xdr\_123456\_cohex\_disch purge;

create table xdr\_123456\_cohex\_disch as

select distinct b.pat\_id

,b.pat\_enc\_csn\_id

,B.DISCH\_DISP\_C

,disp.name as DISCH\_DISP\_C\_name

,B.DISCH\_DEST\_C

,dest.name as DISCH\_DEST\_C\_name

,b.ed\_disposition\_c

,edd.name ed\_disposition

,zps.name as adt\_pat\_status

,b.admit\_conf\_stat\_c

,zcs.name as conf\_stat

FROM xdr\_123456\_cohpat a

join PAT\_ENC\_HSP B on a.pat\_id = b.pat\_id

LEFT OUTER JOIN ZC\_PAT\_CLASS H ON B.ADT\_PAT\_CLASS\_C = H.ADT\_PAT\_CLASS\_C

left outer join ZC\_DISCH\_DISP disp on disp.DISCH\_DISP\_C = B.DISCH\_DISP\_C

left outer join ZC\_DISCH\_DEST dest on dest.DISCH\_DEST\_C = B.DISCH\_DEST\_C

LEFT JOIN zc\_ed\_disposition edd ON b.ed\_disposition\_c = edd.ed\_disposition\_c

left join ZC\_PAT\_STATUS zps on b.ADT\_PATIENT\_STAT\_C = zps.ADT\_PATIENT\_STAT\_C

left join ZC\_conf\_stat zcs on b.admit\_conf\_stat\_c = zcs.admit\_conf\_stat\_c

\--below where clause is customizable per requirements...

\--where regexp\_like(nvl(disp.name,'xxx'),'+(hospice|jail|prison|psych|ama|against med|another facility)+','i')

\-- or regexp\_like(nvl(dest.name,'xxx'),'+(hospice|jail|prison|psych|ama|against med|another facility)+','i')

\-- or regexp\_like(nvl(edd.name,'xxx'),'+(hospice|jail|prison|psych|ama|against med|another facility)+','i')

;

create index xdr\_123456\_cohex\_disch\_patidx on xdr\_123456\_cohex\_disch (pat\_id);

select count(\*), count(distinct pat\_id) from xdr\_123456\_cohex\_disch;

select \* from xdr\_123456\_cohex\_disch order by pat\_id;

# Sex Assigned at Birth

by [Cenan Pirani](https://uclabip.atlassian.net/wiki/people/5f5922bcff2fe2006fb700d8?ref=confluence) on 4/5/2024

*   The following gets the sex assigned at birth for a cohort

select distinct coh.pat\_id

    ,s.name sex\_assigned\_at\_birth

from xdr\_prinv\_coh coh

join patient\_4 p on coh.pat\_id = p.pat\_id

left join zc\_sex\_asgn\_at\_birth s on p.sex\_asgn\_at\_birth\_c = s.sex\_asgn\_at\_birth\_c

;

# Historical Addresses in MSSQL

by [Cenan Pirani](https://uclabip.atlassian.net/wiki/people/5f5922bcff2fe2006fb700d8?ref=confluence) on 8/29/2024

The database and table to access all historical address information (tracking address changes of patients) can be found in ConfomedDims.dim\_RI.PatientDimHistory

Below is an example:

![](data:image/png;base64,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)

# PDF Scans from Clarity

by [Cenan Pirani](https://uclabip.atlassian.net/wiki/people/5f5922bcff2fe2006fb700d8?ref=confluence) on 9/16/2024

Below is the SQL code to retrieve PDF scanned documents for a cohort:

drop table xdr\_prinv\_docs purge;

create table xdr\_prinv\_docs as

select distinct coh.pat\_id

,bb.doc\_info\_id

,bb.doc\_recv\_time

,bb.SCAN\_FILE

,bb.DOC\_DESCR

,bb.DOC\_INFO\_TYPE\_C

,bt.name as doc\_type

\--,BT.DOC\_GROUP

from xdr\_prinv\_coh coh

join DOC\_INFORMATION bb on bb.doc\_pt\_id = coh.pat\_id

left join ZC\_DOC\_INFO\_TYPE bt on BB.DOC\_INFO\_TYPE\_C = BT.DOC\_INFO\_TYPE\_C

WHERE BB.IS\_SCANNED\_YN = 'Y'

and (bb.RECORD\_STATE\_C <> 2 OR bb.RECORD\_STATE\_C IS NULL) -- Deleted

and (bb.DOC\_STAT\_C <> 35 OR bb.DOC\_STAT\_C IS NULL) -- Error

and bb.DOC\_REVOK\_DT is null

;

Once this is created pull the list of files names into a CSV with the query:

select distinct scan\_file from xdr\_prinv\_docs;

Share the file with and notify Wen Ting ([wting@mednet.ucla.edu](mailto:wting@mednet.ucla.edu)), he is the one person that will pull the PDFs and place them in the same share Box.

Use the following for the Subject line and Box folder name: **PODS document request: IRB ##-####: Title - PI**

# PHQ9, PROMIS, SDOH, GAD7, AUDITC

(by [Theona Tacorda](https://uclabip.atlassian.net/wiki/people/557058:34b4a2e8-cda3-430c-9100-db2dd91cd39d?ref=confluence) on 3/18/2025)

– The following obtains data for PHQ9, PROMIS, SDOH, GAD7, AUDITC. When investigators ask for these, developers can deliver flowsheets and/or questionnaires.

\----------------------------------------------------------------------------------------------

\-- Use i2b2.bip\_driver

\----------------------------------------------------------------------------------------------

\--The following DDL defines how this driver was created.

drop table i2b2.bip\_driver purge;

\--create table i2b2.bip\_driver as

\--select distinct 'FLOWSHEET' driver\_source

\-- ,'IP\_FLO\_GP\_DATA.FLO\_MEAS\_ID' driver\_column

\-- ,case

\-- when regexp\_like(gpd.flo\_meas\_name,'+(sdoh|soc.+det)+','i') then 'SDOH'

\-- when regexp\_like(gpd.flo\_meas\_name,'+(phq.+9)+','i') then 'PHQ9'

\-- when regexp\_like(gpd.flo\_meas\_name,'+(promis)+','i') then 'PROMIS'

\-- when regexp\_like(gpd.flo\_meas\_name,'+(GAD7)+','i') then 'GAD7'

\-- when regexp\_like(gpd.flo\_meas\_name,'+(AUDITC)+','i') then 'AUDITC'

\-- end driver\_type

\-- ,gpd.flo\_meas\_id driver\_id

\-- ,gpd.flo\_meas\_name driver\_name

\-- FROM ip\_flo\_gp\_data gpd

\-- where regexp\_like(flo\_meas\_name,'+(sdoh|phq.+9|promis|gad7|auditc)+','i')

\--union

\--select distinct 'QUESTIONNAIRE\_FORM' driver\_source

\-- ,'CL\_QFORM.FORM\_ID' driver\_column

\-- ,case

\-- when regexp\_like(f.form\_name,'+(sdoh|soc.+det)+','i') then 'SDOH'

\-- when regexp\_like(f.form\_name,'+(phq.+9)+','i') then 'PHQ9'

\-- when regexp\_like(f.form\_name,'+(promis)+','i') then 'PROMIS'

\-- when regexp\_like(f.form\_name,'+(GAD7)+','i') then 'GAD7'

\-- when regexp\_like(f.form\_name,'+(AUDITC)+','i') then 'AUDITC'

\-- end driver\_type

\-- ,f.form\_id driver\_id

\-- ,f.form\_name driver\_name

\-- from CL\_QFORM f

\-- where regexp\_like(f.form\_name,'+(sdoh|phq.+9|promis|gad7|auditc)+','i')

\--union

\--select distinct 'QUESTIONNAIRE\_QUESTION' driver\_source

\-- ,'CL\_QQUEST.QUEST\_NAME' driver\_column

\-- ,case

\-- when regexp\_like(quest\_name,'+(sdoh|soc.+det)+','i') then 'SDOH'

\-- when regexp\_like(quest\_name,'+(phq.+9)+','i') then 'PHQ9'

\-- when regexp\_like(quest\_name,'+(promis)+','i') then 'PROMIS'

\-- when regexp\_like(quest\_name,'+(GAD7)+','i') then 'GAD7'

\-- when regexp\_like(quest\_name,'+(AUDITC)+','i') then 'AUDITC'

\-- end driver\_type

\-- ,quest\_id driver\_id

\-- ,quest\_name driver\_name

\-- from CL\_QQUEST

\-- where regexp\_like(quest\_name,'+(sdoh|phq.+9|promis|gad7|auditc)+','i')

\-- order by driver\_source, driver\_type, driver\_id

\--;

\--alter table bip\_driver add constraint bip\_driver\_pk primary key (driver\_source, driver\_type, driver\_id);

\--

\--insert into i2b2.bip\_driver (driver\_source, driver\_column, driver\_type, driver\_id, driver\_name)

\--select distinct 'FLOWSHEET','IP\_FLO\_GP\_DATA.FLO\_MEAS\_ID','SDOH',gpd.flo\_meas\_id, gpd.flo\_meas\_name

\-- from ctsi\_research.lz\_flowsheet\_sdoh\_icl\_zc js --this is from JS SDOH subitem from Kruse 23-5190 (Project\_id=115824)

\-- join ip\_flo\_gp\_data gpd on js.flowsheetrowid = gpd.flo\_meas\_id

\-- left join i2b2.bip\_driver bd on js.flowsheetrowid = bd.driver\_id and bd.driver\_source = 'FLOWSHEET' and bd.driver\_type = 'SDOH'

\-- where bd.driver\_id is null

\--; --210 rows inserted

\--commit;

\----------------------------------------------------------------------------------------------

\-- Using the standard bip driver, get flowsheets and questionnaires data.

\----------------------------------------------------------------------------------------------

\-- Flowsheets

SELECT DISTINCT coh.pat\_id

,enc.pat\_enc\_csn\_id

,enc.inpatient\_data\_id

,rec.fsd\_id

,meas.flt\_id

,meas.flo\_meas\_id

,dta.display\_name

,dta.template\_name

,gpd.disp\_name measure\_name

,gpd.flo\_meas\_name

,meas.meas\_value

,meas.recorded\_time

,MAX(meas.recorded\_time) OVER (PARTITION BY enc.pat\_id, meas.flo\_meas\_id) latest\_measure

FROM xdr\_123456\_coh coh

JOIN i2b2.lz\_clarity\_enc enc ON coh.pat\_id = enc.pat\_id

JOIN ip\_flwsht\_rec rec ON enc.inpatient\_data\_id = rec.inpatient\_data\_id

JOIN ip\_flwsht\_meas meas ON rec.fsd\_id = meas.fsd\_id

JOIN ip\_flo\_gp\_data gpd ON meas.flo\_meas\_id = gpd.flo\_meas\_id

JOIN ip\_flt\_data dta ON meas.flt\_id = dta.template\_id

JOIN i2b2.bip\_driver fd on meas.flo\_meas\_id = fd.driver\_id

and fd.driver\_source = 'FLOWSHEET'

and fd.driver\_type in ('SDOH','PHQ9','PROMIS','GAD7') --Change according to your requirements

\--NOTE: For standard SDOH pulls, use 'SDOH' only unless specifically requested by PI

WHERE meas.recorded\_time IS NOT NULL

AND meas.meas\_value IS NOT NULL

;

\-- Questionnaires

select distinct coh.pat\_id

,f.form\_id

,f.form\_name

,q.quest\_id

,q.quest\_name

,qa.answer\_id

,qa.line

,qa.quest\_answer

,qa.quest\_dat datetime\_answer

from xdr\_123456\_coh coh

join CL\_QANSWER a on coh.pat\_id = a.pat\_id

join CL\_QANSWER\_QA qa on a.answer\_id = qa.answer\_id

join CL\_QQUEST q on qa.quest\_id = q.quest\_id

join CL\_QFORM\_QUEST fq on q.quest\_id = fq.quest\_id

join CL\_QFORM f on fq.form\_id = f.form\_id

join i2b2.bip\_driver qd on (fq.form\_id = qd.driver\_id

and qd.driver\_source = 'QUESTIONNAIRE\_FORM'

and qd.driver\_type in ('SDOH','PHQ9','PROMIS','GAD7') --Change according to your requirements

\--NOTE: For standard SDOH pulls, use 'SDOH' only unless specifically requested by PI

)

or

(fq.quest\_id = qd.driver\_id

and qd.driver\_source = 'QUESTIONNAIRE\_QUESTION'

and qd.driver\_type in ('SDOH','PHQ9','PROMIS','GAD7') --Change according to your requirements

\--NOTE: For standard SDOH pulls, use 'SDOH' only unless specifically requested by PI

)

;

\-- Combine flowsheet and questionnaire results

select x.\* from (

select pat\_id

,'QUESTIONNAIRE' src

, form\_name form\_or\_template\_name

, quest\_name question\_or\_display\_name

, null measure\_name

, to\_char(line) answer\_line\_or\_flow\_measure

, quest\_answer val

, datetime\_answer dt

from xdr\_123456\_quest

union

select pat\_id

,'FLOWSHEET' src

, template\_name form\_or\_template\_name

, display\_name question\_or\_display\_name

, measure\_name measure\_name

, vital\_sign\_type answer\_line\_or\_flow\_measure

, vital\_sign\_value val

, to\_date(vital\_sign\_taken\_time) dt

from xdr\_123456\_flow

) x

order by ip\_patient\_id, sdoh\_date

;

\----------------------------------------------------------------------------------------------

\-- For additional SDOH (Social Determinants of Health), developers can query Social History.

\-- The following obtains SDOH data using Social History.

\-- Many times, social hx does not contain all the information requested for SDOH,

\-- so flowsheets/questionnaires should also be queried.

\----------------------------------------------------------------------------------------------

select distinct coh.pat\_id, coh.study\_id

,soc.contact\_date

,xedu.name education\_level

,zfrs.name financial\_res\_strain

,ziea.name ipv\_emotional\_abuse

,zfear.name ipv\_fear

,zisa.name ipv\_sexual\_abuse

,zipa.name ipv\_physical

,zaf.name alcohol\_freq

,zadpd.name alcohol\_drinks\_p\_day

,zbinge.name alcohol\_binge

,zspouse.name living\_w\_spouse

,zstress.name daily\_stress

,zphone.name phone\_communication

,zsocial.name socialize\_freq

,zchurch.name church\_attendance

,zclub.name club\_mtg\_attend

,zclubm.name club\_member

,zpdpw.name phys\_act\_days\_p\_wk

,zpamps.name phys\_act\_min\_p\_sess

,zfoods.name food\_insec\_scarce

,zfoodw.name food\_insec\_worry

,zmedtr.name med\_trans\_needs

,zothertr.name other\_trans\_needs

from p\_coh coh

join social\_hx soc on coh.pat\_id = soc.pat\_id

left join zc\_edu\_level xedu on soc.edu\_level\_c = xedu.edu\_level\_c

left join zc\_fin\_resource\_strain zfrs on soc.fin\_resource\_strain\_c = zfrs.fin\_resource\_strain\_c

left join zc\_ipv\_emotional\_abuse ziea on soc.ipv\_emotional\_abuse\_c = ziea.ipv\_emotional\_abuse\_c

left join zc\_ipv\_fear zfear on soc.ipv\_fear\_c = zfear.ipv\_fear\_c

left join zc\_ipv\_sexual\_abuse zisa on soc.ipv\_sexual\_abuse\_c = zisa.ipv\_sexual\_abuse\_c

left join zc\_ipv\_physical\_abuse zipa on soc.ipv\_physical\_abuse\_c = zipa.ipv\_physical\_abuse\_c

left join ZC\_ALCOHOL\_FREQ zaf on soc.ALCOHOL\_FREQ\_C = zaf.ALCOHOL\_FREQ\_C

left join zc\_alcohol\_drinks\_per\_day zadpd on soc.alcohol\_drinks\_per\_day\_c = zadpd.alcohol\_drinks\_per\_day\_c

left join zc\_alcohol\_binge zbinge on soc.alcohol\_binge\_c = zbinge.alcohol\_binge\_c

left join zc\_living\_w\_spouse zspouse on soc.living\_w\_spouse\_c = zspouse.living\_w\_spouse\_c

left join zc\_daily\_stress zstress on soc.daily\_stress\_c = zstress.daily\_stress\_c

left join zc\_phone\_communication zphone on soc.phone\_communication\_c = zphone.phone\_communication\_c

left join zc\_socialization\_freq zsocial on soc.socialization\_freq\_c = zsocial.socialization\_freq\_c

left join zc\_church\_attendance zchurch on soc.church\_attendance\_c = zchurch.sdoh\_church\_attendance\_c

left join zc\_clubmtg\_attendance zclub on soc.clubmtg\_attendance\_c = zclub.clubmtg\_attendance\_c

left join zc\_club\_member zclubm on soc.club\_member\_c = zclubm.club\_member\_c

left join zc\_phys\_act\_days\_per\_week zpdpw on soc.phys\_act\_days\_per\_week\_c = zpdpw.phys\_act\_days\_per\_week\_c

left join zc\_phys\_act\_min\_per\_sess zpamps on soc.phys\_act\_min\_per\_sess\_c = zpamps.phys\_act\_min\_per\_sess\_c

left join zc\_food\_insecurity\_scarce zfoods on soc.food\_insecurity\_scarce\_c = zfoods.food\_insecurity\_scarce\_c

left join zc\_food\_insecurity\_worry zfoodw on soc.food\_insecurity\_worry\_c = zfoodw.food\_insecurity\_worry\_c

left join zc\_med\_transport\_needs zmedtr on soc.med\_transport\_needs\_c = zmedtr.med\_transport\_needs\_c

left join zc\_other\_transport\_needs zothertr on soc.other\_transport\_needs\_c = zothertr.other\_transport\_needs\_c

WHERE soc.pat\_enc\_date\_real = (SELECT MAX(soc.pat\_enc\_date\_real) FROM social\_hx soc WHERE soc.pat\_id = coh.pat\_id)

and (soc.edu\_level\_c is not null

or soc.fin\_resource\_strain\_c is not null

or soc.ipv\_emotional\_abuse\_c is not null

or soc.ipv\_fear\_c is not null

or soc.ipv\_sexual\_abuse\_c is not null

or oc.ipv\_physical\_abuse\_c is not null

or soc.ALCOHOL\_FREQ\_C is not null

or soc.alcohol\_drinks\_per\_day\_c is not null

or soc.alcohol\_binge\_c is not null

or soc.living\_w\_spouse\_c is not null

or soc.daily\_stress\_c is not null

or soc.phone\_communication\_c is not null

or soc.socialization\_freq\_c is not null

or soc.church\_attendance\_c is not null

or soc.clubmtg\_attendance\_c is not null

or soc.club\_member\_c is not null

or soc.phys\_act\_days\_per\_week\_c is not null

or soc.phys\_act\_min\_per\_sess\_c is not null

or soc.food\_insecurity\_scarce\_c is not null

or soc.food\_insecurity\_worry\_c is not null

or soc.med\_transport\_needs\_c is not null

or soc.other\_transport\_needs\_c is not null

)

;

# Dispensed Medications

by [Cenan Pirani](https://uclabip.atlassian.net/wiki/people/5f5922bcff2fe2006fb700d8?ref=confluence) on 9/30/2025

\-- STAGE TABLE

DROP TABLE XDR\_PID\_MEDFILLALL PURGE;

CREATE TABLE XDR\_PID\_MEDFILLALL AS

select distinct docs.pat\_id

,ip\_patient\_id

,disp.EXT\_MED\_ERX\_ID

,disp.EXT\_DRUG\_DISP\_INST\_DTTM

,disp.EXT\_DRUG\_DESP

,disp.EXT\_DRUG\_DISP\_AMT

,disp.EXT\_DRUG\_DISP\_UNIT

,disp.EXT\_DRUG\_DOSE\_FORM

,disp.EXT\_MED\_DAY\_SUPPLY

\--,docs.TYPE\_C

,disp.EXT\_DRUG\_PROV\_NAME

FROM xdr\_PID\_pat coh

JOIN DOCS\_RCVD docs on docs.pat\_id = coh.pat\_id

JOIN MED\_DISPENSE disp on docs.document\_id = disp.document\_id

;

\-- PULL

select distinct ip\_patient\_id

,EXT\_MED\_ERX\_ID

,to\_char(EXT\_DRUG\_DISP\_INST\_DTTM, 'mm/dd/yyyy hh24:mi') EXT\_DRUG\_DISP\_INST\_DTTM

,EXT\_DRUG\_DESP

,EXT\_DRUG\_DISP\_AMT

,EXT\_DRUG\_DISP\_UNIT

,EXT\_DRUG\_DOSE\_FORM

,EXT\_MED\_DAY\_SUPPLY

,EXT\_DRUG\_PROV\_NAME

from XDR\_PID\_MEDFILLALL

;
