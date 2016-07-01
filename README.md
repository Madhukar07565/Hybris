# Hybris

## Installation of Hybris
* Download the software from https://support.sap.com/software.html 
* Unzip it to in any srive.
* After unzipping it will have bin and licences folders
* Under bin we have some default extensions
* Bin contains the extension, where as licences folder contains the software licence.
* After all these go to bin folder, and run below command to build the application.
  * setantenv.bat
  * ant clean all  -- It checks the existing folder structure. If any folder structures are missing then it creates them, else it will skip. If there is no build, it will create it from scratch, else it will delete it and re-creates it.
  * ant all -- It assumes that the folder structure already exists. If there is no build it will create it from scratch, else it will modify the build.
* Starting the server
  * hybrisserver.bat
* Default port will be 9001, so to access the application you need to click on http://localhost:9001
* By default hybris will connect to HSQL database, If we want to chane we can add the new database configuration under local.properties file.
* Stopping the server : We need to press the Ctrl+C to stop the server(graceful shutdown)
