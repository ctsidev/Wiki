
# How to obtain DICOM images using AIR

The following describes the process to obtain DICOM images using AIR

In order to be able to access the files you will need to map a drive to the output older from AIR under \\REDLRADADM05294\air (use your T3 credentials)

The actual request starts by asking the Radiology team create a specific project for the extraction. 
Send email to Rose Doherty Rdoherty@mednet.ucla.edu to ask for a project for the extraction with the following subject line: 

`AIR Project Request: XDR_116645_IMG for IRB: 24-5785, Klingbeil`

And provide these data elements:

|  |  |
| ----------- | ----------- |
| Investigator | Klingbeil |
| Programmer | ttacorda |
| IRB | 24-5785 |
| CTSI Preferred project name | XDR_116645_IMG |
| Cohort | 95 |
| Accession Number Count | 3057 |
| Final Storage | ULEAD [kklingbeil_24_24-5785] |


1. Once the project is ready, use CTSI Server to access AIR.
   1. Link to access AIR: https://vitair01.ad.medctr.ucla.edu/ui/search (Credentials are your mednet user name and password.)
1. Prepare an input file with the accession number and MRN for the studies that you wish to request.
1. You need to use the project name created earlier and upload the input file, map accession number and MRN into the AIR fields with the same name.
1. Once you submit the request, the files are going to start appearing into the output folder
1. [trasnferring data to PI is pending] There might be issues with large datasets, pending further discussion with OHIA

> ### Documentation and Supporting materials
> - Link to AIR documentation: https://vitair01.ad.medctr.ucla.edu/docs/ui/docs#/
> - SOP Research IMage Retieval Draft v0.2 CTSI.docx
> - Use Microdicom DICOM Viewer to see the images. The executable can be found in C:\server_apps\MicroDicom-2025.1-win32.exe on the CTSI Server.
