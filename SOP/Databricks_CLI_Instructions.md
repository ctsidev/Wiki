# Databricks CLI Installation

This repository contains instructions on how to download the Databricks CLI in ULEAD and transfer files programmatically from ULEAD into Databricks and vice-versa.

---
## Download and Extract the CLI
1. On your T1 account computer, open the Databricks CLI releases (https://github.com/databricks/cli/releases)
2. Open the latest release and scroll down to the 'Assets' section
3. Download the .zip file that matches ULEAD's operating system and CPU architecture. For standard 64-bit Windows ULEAD environment, download `databricks_cli_<version>_windows_amd64.zip`

## Copy the Executable into ULEAD
1. Inside your ULEAD session, create a dedicated folder to hold the executable. For example: `U:\Arashied\tools\databricks`
2. Copy/Paste the downloaded `.tar.gz` file from Databricks GitHub download and place the file into the above mentioned folder
3. Unpack the `.tar.gz` file in the ULEAD folder and make sure that `databricks.exe` is now available (see example)
   <img width="1020" height="551" alt="image" src="https://github.com/user-attachments/assets/53c62b8e-5f37-4d30-b802-06f8e688b38b" />


## Add the CLI to your ULEAD Path
1. Open AnacondaPrompt from your ULEAD environment
2. Use the following command to set a path to the folder containing your `databricks.exe` file: `set PATH=%PATH%;<your-path-here>` (ex. `set PATH=%PATH%;U:\Arashied\tools\databricks`)

## Import Files and Folders
### Authenticate the CLI
1. Run the following command: `databricks auth login --host https://<your-workspace-url>`
     - Note that `<your-workspace-url>` refers to the URL generated when you do the following: 
         - Open Databricks
         - Navigate to `ohiaADBp001` in the production environment
         - Click on `Workspace` in the left navigation panel then copy that URL into `<your-workspace-url>`
2. Once you run that above command, name your configuration profile when prompted (you can press Enter to accept the DEFAULT profile). For my runs I just use the screename associated with my mednet account (ex. arashied)
3. A brower will then open inside ULEAD prompting you to log in and complete the authentication
4. Verify that the authentication is working by running: `databricks current-user me`
     - If you've done it correctly, this should print your user details

### Uploads
1. Volume paths in the Unity Catalog follow this structure and require the `dbfs:/` prefix for all commands.
2. For `ohiaADBp001` the following path should work for everyone `dbfs:/Volumes/ohiaprd/ucvolume/jsl_deid_file_arrival_base`.
      - Ex. `dbfs:/Volumes/ohiaprd/ucvolume/jsl_deid_file_arrival_base/arashied/<project_folder>`
3. To upload a single file use the following: `databricks fs cp "<ulead_folder_path>/file.csv" dbfs:/Volumes/ohiaprd/ucvolume/jsl_deid_file_arrival_base/ --overwrite`
4. To upload an entire folder directory recursively: `databricks fs cp "<ulead_folder_path>" dbfs:/Volumes/ohiaprd/ucvolume/jsl_deid_file_arrival_base -r --overwrite`
5. To create a new folder inside the volume: `databricks fs mkdir dbfs:/Volumes/ohiaprd/ucvolume/jsl_deid_file_arrival_base/user-folder/new-directory`
6. To list the contents of a volume directory: `databricks fs ls dbfs:/Volumes/ohiaprd/ucvolume/jsl_deid_file_arrival_base -l`

## Export Files and Folders
### Authenticate the CLI
1. Run the following command: `databricks auth login --host https://<your-workspace-url>`
     - Note that `<your-workspace-url>` refers to the URL generated when you do the following: 
         - Open Databricks
         - Navigate to `ohiaADBmld001` in the development environment
         - Click on `Workspace` in the right navigation panel then copy that URL into `<your-workspace-url>`
2. Once you run that above command, name your configuration profile when prompted (you can press Enter to accept the DEFAULT profile). For my runs I just use the screename associated with my mednet account (ex. arashied)
3. A brower will then open inside ULEAD prompting you to log in and complete the authentication
4. Verify that the authentication is working by running: `databricks current-user me`
     - If you've done it correctly, this should print your user details

### Exports
1. Take your de-identified notes and create a spark data frame for each (see example):
   <img width="1085" height="257" alt="image" src="https://github.com/user-attachments/assets/a941390a-4bec-4dd0-ae2a-ac21bcbe5644" />
   
2. I've created a user folder system at the following Unity Catalog Volume: "/Volumes/ohiadev/ctsi/demo" (see example):
   <img width="1193" height="463" alt="image" src="https://github.com/user-attachments/assets/7f91db4d-c750-4e54-89b8-135df24ddf76" />

3. Use the following code to write out de-identified notes/impressions/narratives/pathology/etc. data into your designated user folder
    ```python
    import os
    import glob
    import shutil

    # Update base_path with your intended folder
    base_path = '/Volumes/ohiadev/ctsi/demo/arashied/Savage'

    def write_single_csv(df, filename):
      temp_path = f'{base_path}/_temp_{filename}'
      final_path = f'{base_path}/{filename}.csv'

      # Spark writes a directory
      df.coalesce(1).write.mode('overwrite').option('header', True).csv(temp_path)

      # Find generated CSV part file
      part_file = glob.glob(f'{temp_path}/part-*.csv')[0]

      # Move and rename it
      shutil.move(part_file, final_path)

      # Remove Spark metadata/temp directory
      shutil.rmtree(temp_path)

    for i in range(1, 9):
      df = globals()[f'prov_note_{i}']
      filename = f'savage_provider_notes_{i}'

      write_single_csv(df, filename)
    ```
       - Note 1: df.coalesce will create a new directory nested in your base_path that incudes metadata and .csv file. Shutil then moves the .csv file into the base_path directory and removes the metadata file and temp directory.
       - Note 2: I've written the function to write out 1 file at a time, and then I use the for loop to recursively move through the provider notes files I want to have outputted. You can update the above as you see fit
4. To upload a single file into ULEAD: `databricks fs cp "dbfs:/Volumes/ohiadev/ctsi/demo/<user-folder>/<sub-folder>/<export_file_here>.csv" "<designated_ULEAD_path_here>"`
        - Ex: `databricks fs cp "dbfs:/Volumes/ohiadev/ctsi/demo/arashied/Savage/savage_provider_notes_1.csv" "F:\Inbound\ARashied\PHI Scrubbing\25-0134_Savage\OHIA Results\savage_provider_notes_1.csv"`
5. To upload an entire folder directly into ULEAD: `databricks fs cp "dbfs:/Volumes/ohiadev/ctsi/demo/<user-folder>/<sub-folder>/" "<designated_ULEAD_path_here>" -r`
        - Ex: `databricks fs cp "dbfs:/Volumes/ohiadev/ctsi/demo/arashied/Savage/" "F:\Inbound\ARashied\PHI Scrubbing\25-0134_Savage\OHIA Results" -r`

