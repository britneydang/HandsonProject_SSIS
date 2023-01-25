# Hands on Project 2: SSIS
Project description: This project will mainly focus on SQL Server Integration Services foe ETL purposes.

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
- ETL. Every E and L needs a Connection Manager
- Control Flow:
  - Drag a Data Flow Task from the toolbox into the Control Flow -> double click Data Flow -> select Data Flow Task.
- Data Flow:
  - R-click Connection Managers in the bottom -> New OLE DB Connection... -> New -> server name will be similar to one is input in SSMS earlier, select the same Authentication and Database name "SSIS" -> Ok -> Rename it to SSIS SQL Server
  - ![image](https://user-images.githubusercontent.com/110323703/214494695-4fab8a9c-ce9d-486b-88d9-f4ba3cc765f9.png)
