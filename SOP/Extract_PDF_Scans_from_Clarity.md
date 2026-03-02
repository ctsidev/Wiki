# Extract PDF Scans from Clarity
by @Cenan Pirani on 9/16/2024

## Overview
This is a two step process. First we shall identify the documents and generate a list, then send it to PODS for extraction.

---
### Step 1: Retrieve the document list
Below is the SQL code to retrieve PDF scanned documents for a cohort:


```sql
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
    and (bb.DOC_STAT_C <> 35  OR bb.DOC_STAT_C IS NULL) -- Error
    and bb.DOC_REVOK_DT is null
;
Once this is created pull the list of files names into a CSV with the query:



select distinct scan_file from xdr_prinv_docs;
```

---

### Step 2: Send file to PODS consultatn for extraction

Share the file with and notify Wen Ting (wting@mednet.ucla.edu), he is the one person that will pull the PDFs and place them in the same share Box.

Use the following for the Subject line and Box folder name: 
> PODS document request: IRB ##-####: Title - PI #secure
