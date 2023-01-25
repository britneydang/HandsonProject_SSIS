# Hands on Project 2: SSIS
Project description: This project will mainly focus on SQL Server Integration Services for ETL tasks.

Required instalation:
- SQL Server Developer 2022
- SQL Server Management Studio
- Visual Studio 2022
- SSIS package in VS 2022

Step by step:
Inital set-up:
- Go to SSMS -> create new database -> add database name "SSIS" -> OK -> select newly created database SSIS -> write script to Create Table and Insert Into statements -> Execute.

![image](https://user-images.githubusercontent.com/110323703/214485514-5cdf3d35-3270-4ce9-bc8c-e010249a1ce4.png)
- Go to VS -> create new project -> search for Integration Service Project -> add name and location -> create.
- Click on Solution Explorer -> R-click project folder -> add -> new item -> DtsPackage -> add. Package is the most important window of SSIS
- Open the new Package.Dts[Design]. This contains 5 tabs: Control Flow, Data Flow, Parameters, Event Handlers, Package Explorer
- ETL. Every Extract and Load needs a Connection Manager
- In Control Flow tab (each item in toolbox list called Task):
  - Drag a Data Flow Task from the toolbox into the Control Flow -> double click Data Flow -> select Data Flow Task.
- In Data Flow tab (each item in toolbox list called Component):
  - Create a Connection Manager: R-click Connection Managers in the bottom -> New OLE DB Connection... -> New -> server name will be similar to one is input in SSMS earlier, select the same Authentication and Database name "SSIS" -> Ok -> Rename it to SSIS SQL Server.
![image](https://user-images.githubusercontent.com/110323703/214494695-4fab8a9c-ce9d-486b-88d9-f4ba3cc765f9.png)
  - Create a Source: Drag Source Assistant in -> select Source Type SQL Server and Connection Manager OR Under Other Source, drag OLE DB Source in -> need to connect it to a table -> R-click Edit -> Source Editor pop-up -> Connection Manager: select Data Access Mode and Table Name dbo.[Customer] -> Columns: can select/unselect any columns and edit the column name -> Ok (red X will disappear) -> Click Start to execute. After finishing and view the output, click stop debug to back to design mode.
  - Now need to load data: I want to load data from dbo.[Customer] (selective columns) into a blank table name dbo.Output01.
  - Create a Destination: Drag Destination Assistant in -> select Source Type SQL Server and Connection Manager OR Under Other Destination, drag OLE DB Destination in -> Create a link by connecting a blue line from Source to Destination -> Destination Editor pop-up -> Connection Manager: select Data Access Mode and output table name dbo.[Output01] -> Mapping: check -> Ok. Table dbo.[Output01] in SSMS currently has no data in it. Click Start -> there are 847 rows of data loading from source component to destination component -> stop debugging. Check in SSMS, execute table dbo.[Output01], it is now having 847 records.
![image](https://user-images.githubusercontent.com/110323703/214512969-64feca81-cf41-4b43-8730-0d7c48c8db34.png)
![image](https://user-images.githubusercontent.com/110323703/214513149-99e4c533-10b1-4841-ba4f-9e73c669eb3a.png)
![image](https://user-images.githubusercontent.com/110323703/214513625-a814a7d9-27fc-4087-a640-54382f89285b.png)
  - Moving to Transformation: Why need to transform data? Convert data type for output data: in data flow tab, delete the old link, drag to add the Data Conversion component between Source and Destination component, add links -> 
![image](https://user-images.githubusercontent.com/110323703/214519983-827951ef-b4d2-4840-b92d-36a8f58bc413.png)
 


