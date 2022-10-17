# SqlLogShip Application

### Forked - Changes
The **sqllogship** application does not include parsing for Splunk Ingestors - This fork bridges the two products together. 

### Description: 
The sqllogship application performs a backup, copy, or restore operation and clean-up tasks for a log shipping configuration on a SQL Server database.
- MS.Custom: seo-lt-2019
- MS.Date: 03/14/2017
- MS.Prod: sql
- MS.Prod_Service: sql-tools
- MS.Technology: tools-other
- MS.Topic: conceptual
- Keywords: 
  - "sqllogship"
- MS.AssetID: 8ae70041-f3d9-46e4-8fa8-31088572a9f8
- markingmyname
- maghan
---
### sqllogship Application
The **sqllogship** application performs a backup, copy, or restore operation and associated clean-up tasks for a log shipping configuration. The operation is performed on a specific instance of for a specific database.  
​

  
For the syntax conventions, see Command Prompt Utility Reference. 
## Syntax  
  
```  
  
sqllogship -server instance_name { -backup primary_id | -copy secondary_id | -restore secondary_id } [ -verboselevel level ] [ -logintimeout timeout_value ] [ -querytimeout timeout_value ]  
```  
  
## Arguments  
 **-server** _instance_name_  
Specifies the instance of where the operation will run. The server instance to specify depends on which log-shipping operation is being specified. For **-backup**, *instance_name* must be the name of the primary server in a log shipping configuration. For **-copy** or **-restore**, *instance_name* must be the name of a secondary server in a log shipping configuration.  
  
 **-backup** _primary_id_  
 Performs a backup operation for the primary database whose primary ID is specified by *primary_id*. You can obtain this ID by selecting it from the system table or by using the stored procedure.  
  
The backup operation creates the log backup in the backup directory. The **sqllogship** application then cleans out any old backup files, based on the file retention period. Next, the application logs history for the backup operation on the primary server and the monitor server. Finally, the application runs, which cleans out old history information, based on the retention period.  
  
 **-copy** _secondary_id_  
 Performs a copy operation to copy backups from the specified secondary server for the secondary database, or databases, whose secondary ID is specified by *secondary_id*. You can obtain this ID by selecting it from the stored procedure.  
  
 The operation copies the backup files from the backup directory to the destination directory. The **sqllogship** application then logs the history for the copy operation on the secondary server and the monitor server.  
  
 **-restore** _secondary_id_  
 Performs a restore operation on the specified secondary server for the secondary database, or databases, whose secondary ID is specified by *secondary_id*. You can obtain this ID by using the **sp_help_log_shipping_secondary_database** stored procedure.  
  
 Any backup files in the destination directory that were created after the most recent restore point are restored to the secondary database, or databases. The **sqllogship** application then cleans out any old backup files, based on the file retention period. Next, the application logs history for the restore operation on the secondary server and the monitor server. Finally, the application runs **sp_cleanup_log_shipping_history**, which cleans out old history information, based on the retention period.  
  
 **-verboselevel** _level_  
 Specifies the level of messages added to the log shipping history. *level* is one of the following integers:  
  
|Level|Description|  
|-----------|-----------------|  
|0|Output no tracing and debugging messages.|  
|1|Output error-handling messages.|  
|2|Output warnings and error-handling messages.|  
|**3**|Output informational messages, warnings, and error-handling messages. This is the default value.|  
|4|Output all debugging and tracing messages.|  
  
 **-logintimeout** _timeout_value_  
 Specifies the amount of time allotted for attempting to log in to the server instance before the attempt times out. The default is 15 seconds. *timeout_value* is **int**_._  
  
 **-querytimeout** _timeout_value_  
 Specifies the amount of time allotted for starting the specified operation before the attempt times out. The default is no timeout period. *timeout_value* is **int**_._  
  
## Remarks  
 We recommend that you use the backup, copy, and restore jobs to perform the backup, copy and restore when possible. To start these jobs from a batch operation or other application, call the stored procedure.  
  
 The log shipping history created by **sqllogship** is interspersed with the history created by log shipping backup, copy, and restore jobs. If you plan to use **sqllogship** repeatedly to perform backup, copy, or restore operations for a log shipping configuration, consider disabling the corresponding log shipping job or jobs. For more information, see Disable or Enable a Job](../ssms/agent/disable-or-enable-a-job.md).  
  
 The **sqllogship** application, SqlLogShip.exe, is installed in the x:\Program Files\Microsoft SQL Server\130\Tools\Binn directory.  
  
## Permissions  
 **sqllogship** uses Windows Authentication. The Windows Authentication account where the command is run requires Windows directory access and permissions. The requirement depends on whether the **sqllogship** command specifies the **-backup**, **-copy**, or **-restore** option.  
  
|Option|Directory access|Permissions|  
|------------|----------------------|-----------------|  
|**-backup**|Requires read/write access to the backup directory.|Requires the same permissions as the BACKUP statement. For more information, see.|  
|**-copy**|Requires read access to the backup directory and write access to the copy directory.|Requires the same permissions as the stored procedure.|  
|**-restore**|Requires read/write access to the copy directory.|Requires the same permissions as the RESTORE statement. For more information, see [RESTORE &#40;Transact-SQL&#41;](../t-sql/statements/restore-statements-transact-sql.md).|  
  
> [!NOTE]  
>  To find out the paths of the backup and copy directories, you can run the **sp_help_log_shipping_secondary_database** stored procedure or view the **log_shipping_secondary** table in **msdb**. The paths of the backup directory and destination directory are in the **backup_source_directory** and **backup_destination_directory** columns, respectively.  
  
