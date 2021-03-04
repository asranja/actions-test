# Azure SQL action

## Action repo
https://github.com/Azure/sql-action

## Uses
- For deploying a database schema through a dacpac file 
- For running SQL queries through SQL file

## Limitation
- Supported only in Windows runner today

## Executables used
- **[SqlPackage.exe]**
  - For publishing the database scheme in the `.dacpac` file
  - Doc: https://docs.microsoft.com/en-us/sql/tools/sqlpackage/sqlpackage?view=sql-server-ver15
  - SqlPackage.exe supports many actions like Publish, Export, Import etc. Currently we only support `Publish` action which incrementally updates a database schema to match the schema of a source .dacpac file

- **[SqlCmd.exe]**
  - For running SQL files 
  - Doc: https://docs.microsoft.com/en-us/sql/tools/sqlcmd-utility?view=sql-server-ver15#syntax 

## Action inputs
- Refer [action.yml](https://github.com/Azure/sql-action/blob/master/action.yml)
- Connection string: Semi-colon(;) separated key=value pairs. Custom logic for parsing: https://github.com/Azure/sql-action/blob/master/src/SqlConnectionStringBuilder.ts

## Code flow

- Get Inputs
- Validate Inputs
- Validate Runner Access 
  Check if action runner has access to SQL server. If not, add a temporary firewall rule to allow IP address of runner. This rule will be removed later on.
- Execute Action
  - Publish a Dacpac file using SqlPackage.exe
  - Run a SQL file using SqlCmd.exe

## Files
- main.ts : Entry point for the action. Get Inputs, Check if firewall rule needs to be added and execute action. Remove the firewall rule once action execution is complete
- AzureSqlAction.ts : Contains logic for publishing DACPAC file and running SQL file
- AzureSqlActionHelper.ts : Utility for finding location of SqlPackage.exe and SqlCmd.exe from registry
- FirewallManager.ts : Contains logic for adding/removing firewall rules. 
- AzureSqlResourceManager.ts : Rest client for calling Azure SQL server REST Apis for fetching SQL server details and adding/remove firewall rules
- SqlConnectionStringBuilder.ts : Contains logic to parse the connection string to extract server name, database etc
