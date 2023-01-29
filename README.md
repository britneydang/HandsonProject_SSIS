# Hands on Project 2: ETL Using SSIS
Project description: This project will mainly focus on using SQL Server Integration Services for basic ETL tasks (Data Flows).

Required installation:
- SQL Server Developer 2022
- SQL Server Management Studio
- Visual Studio 2022
- SSIS package in VS 2022

Step by step:
Initial set-up:
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
- Transformation - Data Conversion: Convert data type for output data: in data flow tab, delete the old link, drag to add the Data Conversion component between Source and Destination component, add links -> R-click Dta Conversion -> Edit -> Conversion Editor pop-up -> choose the avaialble input column, select the desired data type for the output column -> change data type for CustomerId from DT_I2 to DT_STR length = 5 -> Ok -> Click Start to Execute/Stop -> Check the newly loaded data in table dbo.[Output02] in SSMS. The input and output properties can be viewed in the Advanced Editor for OLE DB Destination.

![image](https://user-images.githubusercontent.com/110323703/214519983-827951ef-b4d2-4840-b92d-36a8f58bc413.png)
![image](https://user-images.githubusercontent.com/110323703/214768489-7e29d314-ddf5-460d-af3e-d683acbda77a.png) 
- Transformation - Derived Column: in data flow tab, delete the old Data Conversion component, drag Derived Column component in the middle -> edit -> Derived Column editor pop-up -> drag CustomerID in, rename the derived column name CustomerIDNew, select Add New in derived column. Look for the desired data type in Type Casts above and manually type in the Expression -> Type Casts is (DT_STR, length, code_page), type in Expression (DT_STR, 5, 1252)[CustomerID] -> Ok. Need to edit the mapping in OLE DB Destination -> Mappings -> in the input column, switch CustomerID to CustomerIDNew.

![image](https://user-images.githubusercontent.com/110323703/214771792-b2166f9c-1a0b-4297-8180-f7fab668c1fa.png)
- Transformation - Aggregation: Starting with the OLE DB Source component, data source is dbo.Customer -> drag Aggregate component in -> Editor pop-up -> for this task, I want to look at Salesperson and want to have a Count of the Salespersons -> select Salesperson box, Operation = group by -> select ALL box, Operation = count all -> add OLE DB Destination and link -> select appropriate in <ignore> Mappings. To test this -> after running, from 847 rows to 9 rows because of aggregation -> check in SSMS

![image](https://user-images.githubusercontent.com/110323703/215239108-990dd9af-60ee-49f3-b92f-f50ab026dbbf.png)
![image](https://user-images.githubusercontent.com/110323703/215239226-99ff2949-44b8-46a8-b9d0-fe52fb262aea.png)

![image](https://user-images.githubusercontent.com/110323703/215239400-ea45c961-9113-46a7-b70b-797fd3cdc320.png)

![image](https://user-images.githubusercontent.com/110323703/215239411-742328bb-e617-4d1c-b4f4-ac1ca5f10e1b.png)
- Transformation - Multi-Cast: Multi-Cast saves time in reading data and then transmitting it into seperate operations. I want to distribute from one input into more than one outputs: Starting with the OLE DB Source component -> drag Multi-Cast -> drag two OLE DB Destination components (Output01 and Output02) -> Mappings: select appropriate name for <ignore> cell -> Ok

![image](https://user-images.githubusercontent.com/110323703/215240014-dbb1114e-1731-489f-a4fa-aa15efae33b3.png)
- Transformation - Conditional Split: Depend on the content of data, I can split the data from one source into two source with different conditions -> OLE DB Source component -> Conditional Split component -> two OLE DB Destination components (Output01 and Output02) -> edit the Split: in case 1, I want the content only has Title = Mr. and the rest of the content doesnt have Title = Mr. -> In the Conditional Split Editor, drag Title down, in condition [Title]=="Mr.", update output name and default output name (different from the condition) -> add link -> select output name for each OLE DB Destination -> RUN and it has ERRORS. There are errors because the content Title has 7 NULL.

![image](https://user-images.githubusercontent.com/110323703/215241097-d2a67964-a835-4899-9890-7ee3e3ba845b.png)
- Error Handling: Go to Conditional Split Editor -> Configure Error Output -> there are 3 options:
  - Fail Component -> Run -> It will fail
  - Ignore Failure -> Run -> It will run and ignore any failures, the NULL Title will be in the "Title is not Mister" table
  - Redirect Row -> Run -> It will run and eliminate the NULL Title (NULL Title records will not in either tables). I can store these records in the Derived Column -> connect red link from Conditional Split to Derived Column -> run -> 7 NULL Title rows will be in derived column.

![image](https://user-images.githubusercontent.com/110323703/215242401-a868d258-f174-4e34-9a71-58a25df11f35.png)
- Transformation - Row Sampling: Starting with the OLE DB Source component -> Row Sampling component -> two OLE DB Destination components (Selected and Unselected) -> Editor: enter number of rows, specify random seed. Selected table is randomly selected.
- Transformation - Percentage Sampling: Starting with the OLE DB Source component -> Percentage Sampling component -> two OLE DB Destination components (Selected and Unselected) -> enter percentage of rows
- Transformation - Sorting: Sort component can be added after OLE DB Source, added between the Aggregate component and OLE DB Destination -> Sort Transformation Editor -> select input column(s), sort type (asc or desc), and sort order.
- Other Extract - Adding an Excel/Flat file source: Source Assistant -> select Excel/Flat File -> New -> Browse file -> Ok -> Editor -> Connection Manager: select which Sheet -> Ok. If I want to extract a flat file and have an excel destination, I have to add data conversion in the middle.
- Transformation - Union All: two or more input sources go into one output destination. I can union an Excel source and a flat file source together. Remember to add data conversion if needed.
- Transformation - Merge: it is similar to Union All. It allows two inputs but both inputs need to be sorted first before moving to a merge component: Drag two Flat File Source components -> New -> Browse to add text file Source01 and Source02 -> add two Data Conversion to convert AddressID from STR to Integer DT_I4 -> add two Sort components -> Sort Editor -> Sort by AddressID, make sure select correct Pass Through columns, Input Column and Sort Order number -> drag Merge component -> there will be a mapping error from Sort component to Merge component. It happens because I have changed the sources earlier -> R-click the link and Resolve References -> Delete Unmapped Input Column -> go to Merge Editor, select appropriate Merge Input -> drag Derived Column instead of Destination for easier view. Enable Data Viewer to see result. Address ID is sorted in ascending order (data type is integer, not string).

![image](https://user-images.githubusercontent.com/110323703/215305808-515a35d8-e36d-4d57-af16-1c00e9f648cb.png)
![image](https://user-images.githubusercontent.com/110323703/215305832-646fec57-b484-499e-85de-076da368caf1.png)  
![image](https://user-images.githubusercontent.com/110323703/215305685-e722b2b9-3527-431c-8d29-745f942311f2.png)
![image](https://user-images.githubusercontent.com/110323703/215305738-9baae165-2a6f-4db1-b204-f60d0bf26058.png)
- Transformation - Audit: provide additional columns with information if selected in the Audit Transfomation Editor. For example: User Name, Task Name, Machine Name, etc. 
- Other Load: different destinations besides OLE DB, Flat File, Excel, remainders are ADO.NET (used for programming language .NET Framework), Analysis Services Connection Manager (SSAS), File Connection Manager, SSIS Connection Manager.
- Variable is an object, a container that stores a number, a string, a file, a table set.
- Transformation - Row Count: count rows from the result table. Have to use variable in conjunction with Row Count component -> drag Row Count before the result table -> click on Variable cube -> Add -> Name the variable -> Close Variable-> double click to Assign the Row Count to a variable.  

~ COMPLETED ~







 


