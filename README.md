# Hands on Project 2: ETL Using SSIS
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
- Moving to Transformation: Why need to transform data? 
    - Transformation Data Conversion: Convert data type for output data: in data flow tab, delete the old link, drag to add the Data Conversion component between Source and Destination component, add links -> R-click Dta Conversion -> Edit -> Conversion Editor pop-up -> choose the avaialble input column, select the desired data type for the output column -> change data type for CustomerId from DT_I2 to DT_STR length = 5 -> Ok -> Click Start to Execute/Stop -> Check the newly loaded data in table dbo.[Output02] in SSMS. The input and output properties can be viewed in the Advanced Editor for OLE DB Destination.

![image](https://user-images.githubusercontent.com/110323703/214519983-827951ef-b4d2-4840-b92d-36a8f58bc413.png)
![image](https://user-images.githubusercontent.com/110323703/214768489-7e29d314-ddf5-460d-af3e-d683acbda77a.png) 
    - Transformation Derived Column: in data flow tab, delete the old Data Conversion component, drag Derived Column component in the middle -> edit -> Derived Column editor pop-up -> drag CustomerID in, rename the derived column name CustomerIDNew, select Add New in derived column. Look for the desired data type in Type Casts above and manually type in the Expression -> Type Casts is (DT_STR, length, code_page), type in Expression (DT_STR, 5, 1252)[CustomerID] -> Ok. Need to edit the mapping in OLE DB Destination -> Mappings -> in the input column, switch CustomerID to CustomerIDNew.

![image](https://user-images.githubusercontent.com/110323703/214771792-b2166f9c-1a0b-4297-8180-f7fab668c1fa.png)
    - Transformation Aggregation: Starting with the OLE DB Source component, data source is dbo.Customer -> drag Aggregate component in -> Editor pop-up -> for this task, I want to look at Salesperson and want to have a Count of the Salespersons -> select Salesperson box, Operation = group by -> select ALL box, Operation = count all -> add OLE DB Destination and link -> select appropriate in <ignore> Mappings. To test this -> after running, from 847 rows to 9 rows because of aggregation -> check in SSMS

![image](https://user-images.githubusercontent.com/110323703/215239108-990dd9af-60ee-49f3-b92f-f50ab026dbbf.png)
![image](https://user-images.githubusercontent.com/110323703/215239226-99ff2949-44b8-46a8-b9d0-fe52fb262aea.png)

![image](https://user-images.githubusercontent.com/110323703/215239400-ea45c961-9113-46a7-b70b-797fd3cdc320.png)
![image](https://user-images.githubusercontent.com/110323703/215239411-742328bb-e617-4d1c-b4f4-ac1ca5f10e1b.png)
		- Transformation Multi-Cast: I want to distribute from one input into more than one outputs: Starting with the OLE DB Source component -> drag Multi-Cast -> drag two OLE DB Destination components (Output01 and Output02) -> Mappings: select appropriate name for <ignore> cell -> Ok

![image](https://user-images.githubusercontent.com/110323703/215240014-dbb1114e-1731-489f-a4fa-aa15efae33b3.png)









 


