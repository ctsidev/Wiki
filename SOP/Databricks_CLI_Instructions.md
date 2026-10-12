# Databricks CLI Installation

This repository contains instructions on how to download the Databricks CLI in ULEAD and transfer files programatically from ULEAD into Databricks.

---
## Download and Extract the CLI
1. On your T1 account computer, open the Databricks CLI releases (https://github.com/databricks/cli/releases)
2. Open the latest release and scroll down to the 'Assets' section
3. Download the .zip file that matches ULEAD's operating system and CPU architecture. For standard 64-bit Windows ULEAD environment, downalod databricks_cli_<version>_windows_amd64.zip

## Copy the Executable into ULEAD
1. Inside your ULEAD session, create a dedicated folder to hold the executable. For example: 'U:\Arashied\tools\databricks'
2. Copy/Paste the downloaded .tar.gz file from Databricks GitHub download and place the file into the above mentioned folder
3. Unpack the '.tar.gz' file in the ULEAD folder and make sure that 'databricks.exe' is now available

## Add the CLI to your ULEAD Path
1. Open AnacondaPrompt from your ULEAD environment
2. Use the following command to set a path to the folder containing your 'databricks.exe' file: 'set PATH=%PATH%;your-path-here' (ex. set PATH=%PATH%;U:\Arashied\tools\databricks)

## Authenticate the CLI
1. Run the following command: 'databricks auth login --host https://your-workspace-url'
  - Note that 'your-workspace-url' refers to the URL generated when you do the following: 
      - Open Databricks
      - Navigate to 'ohiaADBp001' in the production environment
      - Click on 'Workspace' in the right navigation panel then copy that URL into 'your-workspace-url'
2. Once you run that above command, name your configuration profile when prompted (you can press Enter to accept the DEFAULT profile). For my runs I just use the screename associated with my mednet account (ex. arashied)
3. A brower will then open inside ULEAD prompting you to log in and complete the authentication
4. Verify that the authentication is working by running: 'databricks current-user me'
  - If you've done it correctly, this should print your user details

## Upload Files and Folders
1. Volume paths in the Unity Catalog follow this structure and require the 'dbfs:/' prefix for all commands.
2. For 'ohiaADBp001' the following path should work for everyone 'dbfs:/Volumes/ohiaprd/ucvolume/jsl_deid_file_arrival_base'. You can finish the path with your user/project folder inside jsl_deid_file_arrival_base (ex. 'dbfs:/Volumes/ohiaprd/ucvolume/jsl_deid_file_arrival_base/arashied')
3. To upload a single file use the following: databricks fs cp "./file.csv" dbfs:/Volumes/ohiaprd/ucvolume/jsl_deid_file_arrival_base/ --overwrite
4. To upload an entire folder directory recursively: databricks fs cp "./my_folder" dbfs:/Volumes/ohiaprd/ucvolume/jsl_deid_file_arrival_base -r --overwrite
5. To create a new folder inside the volume: databricks fs mkdir dbfs:/Volumes/ohiaprd/ucvolume/jsl_deid_file_arrival_base/user-folder/new-directory
6. To list the contents of a volume directory: databricks fs ls dbfs:/Volumes/ohiaprd/ucvolume/jsl_deid_file_arrival_base -l
