# Extract JPG Images from Clarity
by @Javi Sanz on 3/02/2026

## Overview
This is a two step process. First we shall identify the images and generate a list, then create a SN ISS ticket and contact the BLOB Image Server team for extraction.

---
### Step 1: Retrieve the document list
Below is the SQL code to retrieve JPG image documents for a cohort:


```sql
drop table xdr_prinv_imgs purge;
create table xdr_prinv_imgs as
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
    and (bb.DOC_STAT_C <> 35  OR bb.DOC_STAT_C IS NULL) -- Error
    and bb.DOC_REVOK_DT is null
;


-- Once this is created pull the list of files names into a CSV with the query:
-- Save output as [##-####_PI.csv]
select distinct scan_file from xdr_prinv_imgs where doc_info_type_c = '900001'; -- Clinical Photo
```

---

### Step 2: Create Service Now ISS ticket 

1.-Go to ISS Service Now and create a ticket
> Subject: CTSI request for dermatology images for research:  IRB ##-####: Title - PI
> 
> Message:
> 
> My name is XXXXXXXX and I work with the CTSI Honest Broker team for medical research and I have a request for a feasibility check on some images from the dermatology dept.
> Could you pull them for me? 

Don't fgorget to upload the file to the ticket [##-####_PI.csv].

2.-Notify Kyle McLeod (KyleMcLeod@mednet.ucla.edu), he is the one person that will pull the files.
> Subject: CTSI request for dermatology images for research:  IRB ##-####: Title - PI
> 
> Message:
>
> Hi Kyle.
>
> My name is XXXXXXXX and I work with the CTSI Honest Broker team for medical research and I have a request for a feasibility check on some images from the dermatology dept.
> Could you pull them for me? 

> The ISS ticket # is TIX05570604

> Thanks

Use the following for the Subject line and Box folder name: 
> PODS document request: IRB ##-####: Title - PI #secure
