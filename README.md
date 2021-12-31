# Oracle_Bash_Scripts
Collection of bash scripts used for database maintenance and monitoring  

These are various bash scripts I have written to help me monitor and maintain Oracle 11g, 12c, and 19c databases on AIX and Linux systems.

1. [tablespace_growth_trender.sh](/scripts/tablespace_growth_trender.sh) : This remotely checks the size of every tablespace and the creation date of every datafile in multiple databases and records the data in a database table to trend tablespace growth over time. 

2. [alert log monitor](/scripts/alert_log_monitor/) : The [alert_log_monitor.sh](/scripts/alert_log_monitor.sh) script will scan the database alert log of 11g+ Oracle databases for ORA- errors and instance terminations / restarts, sleep 5 minutes, then scan again. An email will be sent to alert the DBA's if anything is found within the past 5 minutes. The [alert_log_CRON_mgr.sh](/script/alert_log_CRON_mgr.sh) script is scheduled in crontab to verify that the alert_log_monitor.sh is running and restart it if it is not. An email will be sent to alert the DBA's if alert_log_monitor.sh was abnormally terminated. 

3. [11g_BATCH_audit_maintenance.sh](/scripts/11g_BATCH_audit_maintenance.sh) : If the AUD$ table has grown very large, this script will archive and purge records in small batches to reduce execution time and to not overwhelm resources. It first purges everything older than 18 months from AUD$ in small batches. It then archives and purges audit records from aud$ that are older than 45 days in small batches. An archive table will need to first be created before this script can successfully execute. After execution, AUD$ records older than 45 days will have been archived, and records older than 18 months will have been deleted.  

4. [11g_audit_maintenance.sh](/scripts/11g_audit_maintenance.sh) : This script performs Audit Trail maintenance for an 11g database. Scheduling this script to run daily for each DB will keep a 45 day rolling window of data in the AUD$ table, a rolling 1.5 year's worth of data in the audit archive table, a rolling 15 day window in the ASM and DB AUDIT_FILE_DEST on the OS on each node in the cluster, and a rolling 1.5 year's worth of AUDIT_FILE_DEST archives. Audit records older than 45 days will first be archived into an archive table before being purged from the AUD$ table. ASM and DB AUDIT_FILE_DEST OS files that are older than 15 days are archived to a zip file on an NFS mounted FS and then deleted from the AUDIT_FILE_DEST. 

5. [19c_audit_maintenance.sh](/scripts/19c_audit_maintenance.sh) : This script performs Unified Audit Trail maintenance for a 19c standalone or 2-node RAC database. Scheduling this script to run daily will keep a 45 day rolling window of data in the UNIFIED_AUDIT_TRAIL table and a rolling 1.5 year's worth of data in the audit archive table. Audit records older than 45 days will first be archived into an archive table before being purged from the UNIFIED_AUDIT_TRAIL table. Any OS Spillover Audit Records on each node of a standalone or 2-node RAC cluster will also be moved into the Unified Audit Trail and deleted from the OS.

6. [database_workload_monitor.sh](/scripts/database_workload_monitor.sh) : This script calculates the average number of total 'User Calls' and DML transactions ('User Commits' and 'User Rollbacks') that have occured in the database per hour since the last startup time to trend over time with the purpose of determining if the database's workload is increasing or decreasing over time and to pin point peak hours and/or days. 

7. [push_remote_scripts.sh](/scripts/push_remote_scripts.sh) : This script will push a local file or files (any number of files) to a list of remote servers. The purpose of this script is to create a central repository for maintenance scripts. Some scripts are required to be stored locally on the machine the script needs to be executed on. When changes need to be made to those scripts, each script on each server would have to be individually changed. To prevent that, the script(s) can be stored on a local machine (the central repository) and then just copied over to all of the other servers by using this script. Any changes to those scripts can be done on the local machine, i.e. the central repository, then pushed out using this script. Therefore, only one server and one file will need to be touched instead of multiple. 

8. [it_reaches_out.sh](/scripts/availability_monitor/it_reaches_out.sh) : This script monitors the availability of the Oracle database servers stored in the database table: oracle_servers and the Oracle environments residing on each of the servers by remotely executing the script: [oracle_availability_check.sh](/scripts/availability_monitor/oracle_availability_check.sh). An alert will be emailed to the DBA's if a server, database instance, ASM instance, or if the LISTENERS is/are offline and/or unavailable. An alert will also be emailed to the DBA's if the VIP is not running on the correct machine. This script also collects various information about each environment to populate a dashboard that summarizes the entire Oracle environment.
