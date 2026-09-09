# CTSI ULEAD Data Transfer Instructions

The link to the full guide with screen shots from OHIA can be found
[here](https://uclahs.app.box.com/s/xr9qy9pipvl4rbfj549pwx1cuks8gung)


**STEP 1: Copy data into PI IRB ULEAD folder**

Navigate to the location inside ULEAD: F:\\Data Transfer
Folders\\Inbound\\ctsi and add a new folder.

The naming convention for folder is ‘\<PI AD username\>\_\<current year
- 2 digit\>\_\<IRB\>’. Example: MLechner\_21\_20-000857.

**STEP 3: Data transfer into ULEAD. As of 1/17/2023 the programmers complete this.**

Open [ULEAD ](https://ulead.mednet.ucla.edu/)

Set it up app to open using Citrix instead of Web Browser by going to
Settings \> Advanced \> Using Citrix Workspace
App

![](images/media/image1.tmp)![](images/media/image2.tmp)![](images/media/image3.tmp)

Go back to Home and open ULEAD File Explorer

![](images/media/image4.tmp)

At this time, the system will open a File Explorer just like it does
when looking at your own file system but seeing the ULEAD internal file
system instead.

![](images/media/image5.tmp)

Navigate to Data Transfer Folders \\ Inbound \\ CTSI and find the PI
folder according to the specs the naming convention for it which is
‘\<PI AD username\>\_\<current year - 2 digit\>\_\<IRB\>’. Example:
MLechner\_21\_20-000857 and copy and paste the files into their final
destination. Remember to unzip the files if necessary.

**STEP 4: Contact PI. As of 11/9/2022 and possibly earlier, the
programmers complete this.**

After the data has been copied into ULEAD, compose an email to the PI
and CC the consultant and myself in it. They are already aware that
access to ULEAD and data sharing might happen at different points in
time. There is work being done to coordinate this but for the time
being, we communicate the PI when their data is available.

Example:

***Subject:***

ULEAD Data Transfer Complete - Bell IRB\#:23-000516

***Message:***

Your data has now been provisioned. Your data has now been provisioned.
Please note that we require your review of this data within the next two
weeks.  At this time, if you discover any discrepancies, we will make
every attempt to prioritize your findings and resolve any issues. 
However, if no feedback is provided within this timeframe, we will
conclude your project. Any requests or findings afterwards will result
in a new project.  Thank you for your understanding.

You can find it within ULEAD in the ShareFile application at the
following path:  
F:\\Inbound\\CTSI\\DBell\_23\_23-000516

Along with your data files, you shall find a data dictionary that
outlines the meaning of every file and every field and how you can
connect records across files using different keys/IDs for patients,
encounters, providers, etc...


ULEAD SUPORRT Inforamtion

This is the [ULEAD OHIA IT site](https://it.uclahealth.org/about/ohia/products/ulead) with all the information foor this product.

Support highlights
* For any questions about ULEAD, please submit a [ServiceNow ticket](https://uclahsprod.service-now.com/it_portal?id=sc_cat_item&sys_id=3f1dd0320a0a0b99000a53f7604a2ef9)
* You are also welcome to attend ULEAD Office Hours, held every Tuesday from 2:00 – 2:30 pm and Thursday from 9:00 – 9:30 am, where the support team is available to answer questions, troubleshoot issues, and provide guidance.
* Please contact Self Service Analytics or Customer Care at 310-267-CARE (2273). Specialists are available 24/7 to provide support.
* Useful links:
*     If you have not already accessed ULEAD and ShareFile you can find instructions in  [How to Request Access to ULEAD](https://uclahs.app.box.com/file/1670923043323?s=22fc9r33x1lw1h5t6cyznp7juczpv8kt)
*     [ULEAD UserGuide](https://uclahs.app.box.com/file/997096403967?s=i5wkyg251tfivo03wu3wb4yvg776q2jb)
*     [ULEADFAQs](https://uclahs.app.box.com/file/985594259779?s=d45cd2y3dmg9f8jbgp1fcte3oin9kd9x)
*     [JMP Pro License Activation TipSheet](https://uclahs.app.box.com/file/2013049893064?s=i9gtvrpr7s2v4qdr5u1e5p43povkogc5)


One last tip, oftentimes investigators can't find the data when opening
it with Excel. This is most typically due to  
"file type" selection filter. You must change it to “All Files” to be
able to see the dataset
files.

![](images/media/image6.tmp)

# **Requesting Data Transfer out of ULEAD (Outbound)**

### For cases when data needs to be transferred from the PI’s ULEAD back to yourself (Data Analyst):

Investigators are ale to do their own extraction following the
instructions below. We should ONLY do it for them when it’s fully
justifieid and there are additional circumstance

1.  Open ULEAD app

2.  Open ULEAD File Transfer app.

> ![](images/media/image7.tmp)![](images/media/image8.tmp)

3.  Ask PI for the following information (questions are also on the
    screen opened in \#2).
    
    1.  Filename and location
    
    2.  Reason - in one or two sentences, please describe the purpose of
        your export
    
    3.  IRB\# (This can be obtained on your own)
    
    4.  Are any of the files larger than 2GB?
    
    5.  What is the estimated number of patient records?
    
    6.  Do any of the files contain any of the following?
        
        1.  PHI (Protected Health Information)
        
        2.  RI (Restricted Information)
        
        3.  Data related to research
        
        4.  Data specifically related to NPH or addiction clinics
        
        5.  Data specifically related to mental health or substance
            abuse
    
    7.  Will any of this data be shared outside of your department?
    
    8.  Are any of the files being exported for a 3<sup>rd</sup> party?
    
    9.  Will this data be used for publication, mailing list,
        fundraising, or marketing purposes?

4.  Once you obtain all the information in \#3 and verify accuracy,
    submit the request and upload the file(s) to transfer.

5.  You will receive an email with instructions to download the file(s).
    Example below.

> ![](images/media/image9.tmp)

6.  You can retrieve the file(s) from your Downloads folder after
    clicking on the link in \#5.

Ultimately, you can deliver the files to the
investigators.

# **Requesting Data Transfer into ULEAD (Inbound)**

### For cases when the research team has data outside ULEAD and wishes to move it inside it to leverage it for their work.

1.  We shall received a notification from the PI or their team,
    preferably through the same email thread used during data
    provisioning.

2.  We will ask some basic questions
    
    1.  File(s) location: (Ask them to share it with us through a Box
        folder).
    
    2.  File(s) name:
    
    3.  Brief description of the data:
    
    4.  Does it contain PHI:
    
    5.  Size: it case it can represent a challenge to work with it in
        the platform.

3.  Open [ULEAD](https://it.uclahealth.org/about/ohia/products/ulead)
    and use the same protocol as with Step 3 for the data provisioning
    project.

4.  Notify the team (including consultant) that the data has been copied
    into ULEAD using the same message that we use for standard
    provisioning.

# **What to do when the PI team asks to add new members to their ULEAD space/folder**

### For cases where the PI team asks to add new members of their team to their ULEAD space/folder.

1.  Reply to the PI including the consultant in charge of the request to
    confirm that the new members are in the IRB and let the consultant
    address the
request,

# **Providing Support Information to users**

### For cases where the user(s) asks for additional assistance (I can’t see my data), please forward the following information to them:

 

Hi. 

I’d suggest taking a look at the [ULEAD User
Guide](https://uclahs.app.box.com/s/i5wkyg251tfivo03wu3wb4yvg776q2jb)
where you can find the information on how to access your data and
perform other ULEAD related activities. Bear in mind that you’d need to
be on campus or connected to the University VPN network in order to
access ULEAD. You might also [See this
video](https://uclahs.box.com/s/24h1x2xgb5ecj8yv9i28vv5f8uudscix) on how
to access your data.  ULEAD/OHIA host weekly office hours to assist
users with these kind of issues, Please feel free to attend at the
following dates/times

  - Mondays 9-9:30: <https://uclahs.zoom.us/j/95416625765>

  - Thursday 2-2:30: <https://uclahs.zoom.us/j/92818491021>

Additionally, you may also email the ULEAD team support directly and
relate to them the issues that you are having, and they will assist you.
ULEAD Support Team \<<ULEADSupportTeam@mednet.ucla.edu>\>

And finally, there se are some other useful links for ULEAD provider by
OHIA

·         [Application
Catalog](https://uclahs.box.com/s/qiq0uov62775q3pzh0xp6j7bweugouih)

·         [FAQ
Page](https://uclahs.box.com/s/d45cd2y3dmg9f8jbgp1fcte3oin9kd9x)

·         [Published Release
Notes](https://uclahs.box.com/s/k2cpse5gdf0rbo5z4pr86jokjuc5xhu0)

·         [OHIA
Portal](https://it.uclahealth.org/about/ohia/ohia-products/ulead)  -This
should have the date, times and Zoom links to Office Hours

Thank you and best regards

PS: One common issue when ‘not seeing the data’ is to change the File
type selection when opening files using Excel to “All FIles”

![](images/media/image10.tmp)

## Overall CTSI workflow as general context (2/23/23)

The tasks assigned to the CTSI teams should be done in the order below,

1.  **Researchers submits a new data request to CTSI** for an approved
    IRB  (Researchers)

2.  **CTSI Admins submits ULEAD access request** (Service Now ticket) on
    behalf of researchers (SLA:2 weeks) (I have moved this to the top
    because we like to work on the data extraction while the UELAD
    access is being worked on) (Consultants)

3.  **ISS?OHIA creates a new IRB folder inside ULEAD** (provisioning the
    data already part of \#3) (Programmers)

4.  **CTSI transfers the requested files into ULEAD folders** (manual
    copy or by submitting a data transfer ticket) (Programmers)

5.  OHIA provisions ULEAD access (Tools and IRB folder access) and
    notifies CTSI/PI

6.  CTSI notifies the users & provides ULEAD instructions (User Guide
    and FAQ, Folder Name) This breaks down into two items which is where
    the main issue is
    
    1.  **CTSI communicates Researchers that ULEAD access has been
        granted** (Consultants)
    
    2.  **CTSI communicates Researchers that data has been delivered**
        (Programmers)

**Legacy: Request data transfer into ULEAD. As of 11/9/2022 and possibly
earlier, the DO NOT programmers complete this.**

Submit a service request. Add required info, and put Sanz, Javier in the
***Customer Watch List*** section.

Put the information in the following fields noting the current date and
the folder title:

***Short Description:***

ULEAD Data Transfer Request - 8/10/21 - MLechner\_21\_20-000857

***Description:***

Please assign to OHIA Enterprise Data & Analytics Platform:

Please transfer content from:

\\\\czohsaulwcoep01.file.core.windows.net\\coesharep01\\Data Transfer
Folders\\Inbound\\ctsi\\MLechner\_21\_20-000857

Into:

\\\\CZOHVCXPROFSP01\\Data Transfer
Folders\\Inbound\\CTSI\\MLechner\_21\_20-000857
