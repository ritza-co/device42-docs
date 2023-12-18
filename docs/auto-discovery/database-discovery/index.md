---
title: "Database Discovery"
---

## About Database Discovery

Database discovery was introduced in v16.02.00 _(ensure you are running v16.02+)_, and currently supports Windows and \*nix-based discovery jobs to discover Microsoft SQL (aka MSSQL), Oracle, Postgres SQL, and DB2 databases.

:::note
Database discovery requires an **Application Dependency Mapping** license. Go to **Tools > Settings > Licensing** to see if the license is enabled. Contact [support@device42.com](mailto:support@device42.com) for licensing assistance.
:::

As of v16.17.00, Device42 supports MSSQL and Oracle database discovery for customers that have database instances configured to listen on non-standard ports, especially on a shared database server hosting multiple instances. During database discovery, Device42 will identify and connect through the discovered active listening port.

You can also use [Database Connections Discovery](#database-connections-discovery-jobs) jobs (see below) to discover databases.

## MS SQL Server DB Discovery (on Windows targets)

Microsoft SQL Server (MSSQL) discovery is supported on discovery targets running Microsoft Windows, and requires a separate set of credentials to authenticate to the database instance itself. Ensure these credentials have appropriate permissions to view the databases you are interested in discovering.

Device42 supports autodiscovery on Windows and \*nix platforms for the following MSSQL versions.

- MSSQL 2005
- MSSQL 2008
- MSSQL 11 on SQL Server 2012
- MSSQL 12 on SQL Server 2014
- MSSQL 13 on SQL Server 2016
- MSSQL 14 on SQL Server 2017
- Cluster MSSQL 12

### Minimum Permissions Requirements for MSSQL Discovery

In order to query the below tables, please ensure you have **View Server State** permissions. For discovery to return detailed info about your database instance, you will require read permissions to the following system views:

<table><tbody><tr><td width="288"><ul><li>sys.dm_exec_connections</li><li>sys.dm_exec_sessions</li><li>sys.databases</li><li>sys.master_files</li></ul></td><td width="288"><ul><li>sys.tables</li><li>sys.dm_os_sys_info</li><li>sys.dm_os_sys_memory</li><li>sys.all_objects</li></ul></td></tr></tbody></table>

The snippet shown below is necessary to get data from some of the preceding views even if the user has read permissions (_sys.master\_files_, for example).

1 use \[master\]
2 GO
3 grant VIEW any DEFINITION TO \[discovery\_user\]
4 GO

**Note**: The discovery user must belong to the administrators user group to discover databases successfully.

### Setting up your MSSQL discovery job

1) To begin discovering your MSSQL Databases \[running on Windows\], create a new Windows discovery job and be sure to enable database discovery by checking the **Collect database server information** checkbox. Select **Allow Unencrypted Connections** if you want to discover MSSQL 2005 or MSSQL 2008 databases.

![](/assets/images/D42-21939_Unencrypted-conns-for-SQL-db-2.png)

Be sure to fill out both sets of credentials – the first set (_Database Username / Password_) to authenticate to the Microsoft SQL database itself and the second (_Discovery Targets Credentials_) to authenticate to the Windows server itself (like you’re used to for [regular Windows-based discovery targets](auto-discovery/windows-and-hyper-v-auto-discovery.md)).

You can enter an ordered list of preferred _Discovery Target(s) Credential(s)_ when you create a database discovery job. When the job runs, it will use the credentials in the order in which you enter them, stopping at the first successful authentication. Subsequent job runs use the last successful credential and then the remaining credentials in the ordered list.

![](/assets/images/D42-21939_Unencrypted-conns-for-SQL-db-3-preferred-creds.png)

* * *

![Example job status](/assets/images/discovery-job-status-trimmed.png)

2) Kick off your new discovery job to test it (select "Run now" on the Job Screen). As MSSQL databases are detected, discovery will import a list of all instances, databases, and connection details. You can see the status of the discovery job on the job's setup page, as well - Scroll down to the "Job Status" section and look for the bar graphs.

### Viewing MSSQL discovery job results

3) Once the job finishes, you can view the results of your database discovery.

There are a couple different ways to see the database details; the most direct is via the discovered MSSQL Application components themselves. Head to the Device42 main menu, Apps menu -> Application Components. If you don't see your SQL Server instances right at the top of the list, you can search for "SQL" to narrow the list down.

You can see the newly discovered SQL Server instances in the example below -- Click any one of the Application Component Names for more details: ![MSSQL Application Components](/assets/images/Apps-Application-Components-menu-annotated.png)

Scroll to the bottom, and click the database instance name to view database details: ![](/assets/images/click-to-view-SQL-details.png)

For a rundown of the database details discovery provides, jump to the ["Available SQL database instance information"](#available-sql-database-instance-information) section.

### Another way to view SQL Database details

Results are also available by browsing to the discovered Windows server instance's CI; either search for the device from the dashboard, via "Devices -> All Devices", or view your discovery job's results / score page and jump to the servers from there: ![](/assets/images/click-success-for-discovery-scores.png)

Click any of the "device names" (right hand side) to jump to each discovered machine's details view. From the details view, you can simply click the "Services" tab to view a list of services, and can then click on the Microsoft SQL Server instance in that list. ![](/assets/images/discovery-score-to-view-1.png)

### Available SQL database instance information

On the "View Database Instance" screen, you will see lists containing the following items:

- Databases on that instance _(each clickable for individual DB connection & size stats)_
- Connections to that DB instance _(connection details available for each connection)_
- Database size details, creation date, and path
- Extra details, including server start time, CPU count, Memory info, and more

If your environment includes SQL cluster databases, discovery returns the following information about the clusters:

- SQL Cluster name
- SQL Cluster Node Role
- Is Node Active or Read-Replica
- Does Passive Node use Log Ship
- Does Passive Node use DB Mirroring

A snippet from each section of the page can be seen below: ![](/assets/images/database-list.png) ![](/assets/images/DB-Connection-Details.png) ![](/assets/images/database-size-details.png) ![](/assets/images/extra-DB-details.png)

## Oracle DB Discovery (on Windows and \*nix targets)

Oracle database discovery (introduced in v16.04.00) is supported for Windows an \*nix discovery targets, and requires a separate set of credentials to authenticate to the database instance itself. Ensure these credentials have appropriate permissions to view the databases you are interested in discovering.

As of v16.14.01, Device42 now supports autodiscovery on Windows and \*nix platforms for the following Oracle database versions.

**Oracle 10g**

- Windows 32 bit
- CentOS 32 bit

**Oracle 11g**

- Windows 32 bit
- Windows 64 bit
- CentOS 32 bit
- CentOS 64 bit

**Oracle 12c**

- Windows 64 bit
- CentOS 64 bit

**Oracle 18c**

- CentOS 64 bit
- Windows 64 bit

**Oracle 19c**

- Windows 64 bit
- CentOS 7 64 bit

As of v16.17.00, Device42 database autodiscovery for Windows and \*nix targets supports discovery for Oracle RAC clustered database environments. This will help users better assess their cluster databases and will enable users to better understand all IT assets that are tied to critical business applications. Discovery returns data about the RAC configuration, the RAC database, and the nodes (physical servers) running the RAC software. You can run the autodiscovery against one or more nodes in the Oracle RAC and return information about all connected nodes. We require the use of sudo for Oracle discoveries, without it lockout is a risk.

### Minimum Permissions Requirements for Oracle Discovery

For discovery to return detailed info about your database instance, you will require read/view permissions for the following system views/tables:

<table><tbody><tr><td width="288"><ul><li>V$SESSION</li><li>V$DATABASE</li><li>DBA_SEGMENTS</li><li>DBA_OBJECTS</li></ul></td><td width="288"><ul><li>SYS.ALL_USERS</li><li>DATABASE_COMPATIBLE_LEVEL</li><li>SYS.PRODUCT_COMPONENT_VERSION</li></ul></td></tr></tbody></table>


To get information about Pluggable Databases (PDBs) within a Container Database (CDB) in Oracle for non-DBA users, two key permission configurations are required:

- SELECT permission on the V_$CONTAINERS view.
- Set `container_data=all container=current` for context configuration.

### Setting up your Oracle discovery job

1) To begin discovering your Oracle databases, create a new discovery job for Windows or \*nix (or both) targets, and be sure to check the **Collect database server information** checkbox.

Be sure to fill out both sets of credentials – the first set (_Database Username / Password_) to authenticate to the Oracle database itself and the second (_Discovery Targets Credentials_) to authenticate to the Windows or \*nix server itself.

You can enter and ordered list of preferred _Discovery Target(s) Credential(s)_ when you create a database discovery job. When the job runs, it will use the credentials in the order in which you enter them, stopping at the first successful authentication. Subsequent job runs use the last successful credential and then the remaining credentials in the ordered list.

![](/assets/images/D42-21939_Oracle-db-discovery.png)

2) Kick off your new discovery job to test it (select **Run Now** on the Jobs screen). As Oracle databases are detected, discovery will import a list of all instances, databases, and connection details.

![](/assets/images/Oracle-Discovery-1a-3.png)

### Viewing Oracle DB discovery job results

3) Once the job finishes, you can view the results of your database discovery. There are a couple different ways to see the database details; the most direct is via the discovered Oracle Application components themselves. On the the Device42 main menu, select **Apps -> Application Components**. If you don't see your Oracle DB instances right at the top of the list, you can search for _Oracle_ to narrow down the list.

![](/assets/images/Oracle-Discovery-2a-4.png)

Use the View Database Instances screen to view a list of discovered databases.

![](/assets/images/Oracle-Discovery-7-DB-Instance.png)

Scroll down through the screen to see a list of database connections and sizes.

![](/assets/images/Oracle-Discovery-7a-DB-Connections.png)

![](/assets/images/Oracle-Discovery-7b-DB-Sizes.png)

You can also click on the database name on the View Database Instances screen to see its connection and size data.

![](/assets/images/Oracle-Discovery-7c-DB-ByName.png)

On the Application Components screen, click **Show** to view the database Dependency Chart or an Impact Chart or List.

![](/assets/images/Oracle-Discovery-5-Dependency-Chart.png)

![](/assets/images/Oracle-Discovery-3a-4.png)

## Postgres SQL DB Discovery (on \*nix targets)

Postgres SQL database discovery (introduced in v17.11.00) is for \*nix discovery targets, and requires a separate set of credentials to authenticate to the database instance itself. Ensure these credentials have appropriate permissions to view the databases you are interested in discovering.

### Minimum Permissions Requirements for Postgres SQL Discovery

For discovery to return detailed info about your database instance, you will need access to tables:

* pg_database
* pg_tablespace
* pg_stat_activity
* init_server_addr (function)

### Setting up your Postgres SQL discovery job

1) To begin discovering your Postgres SQL databases, create a new discovery job for \*nix targets, and be sure to enable database discovery by checking the **Collect database server information** checkbox.

Be sure to fill out both sets of credentials – the first set (_Database Username / Password_) to authenticate to the Postgres database itself and the second (_Discovery Targets Credentials_) to authenticate to the \*nix server itself.

You can enter and ordered list of preferred _Discovery Target(s) Credential(s)_ when you create a database discovery job. When the job runs, it will use the credentials in the order in which you enter them, stopping at the first successful authentication. Subsequent job runs use the last successful credential and then the remaining credentials in the ordered list.

![](/assets/images/D42-23580_Postgres-ad-job-edit-page.png)

2) Kick off your new discovery job to test it (select **Run Now** on the discovery jobs screen). As Postgres databases are detected, discovery will import a list of all instances, databases, and connection details.

### Viewing Postgres SQL DB discovery job results

3) Once the job finishes, you can view the results of your database discovery. There are a couple different ways to see the database details; the most direct is via the discovered Postgres application components themselves. On the the Device42 main menu, select **Applications -> Application Components**. If you don’t see your Postgres DB instances right at the top of the list, you can search for _Postgres_ to narrow down the list, or filter the list **By Category > Database**.

![](/assets/images/postgresSQL.jpg)

You can also select **Resources > Databases > On-Prem Databases** to display a list of databases.

![](/assets/images/AD2-700x407.png)

## DB2 Discovery (on \*nix targets)

DB2 database discovery (introduced in v18.02.00) is for \*nix discovery targets, and requires a separate set of credentials to authenticate to the database instance itself. Ensure these credentials have appropriate permissions to view the databases you are interested in discovering.

### Setting up your DB2 discovery job

1) To begin discovering your DB2 databases, create a new discovery job for \*nix targets, and be sure to enable database discovery by checking the **Collect database server information** checkbox.

Be sure to fill out both sets of credentials – the first set (_Database Username / Password_) to authenticate to the DB2 database itself and the second (_Discovery Targets Credentials_) to authenticate to the \*nix server itself.

You can enter and ordered list of preferred _Discovery Target(s) Credential(s)_ when you create a database discovery job. When the job runs, it will use the credentials in the order in which you enter them, stopping at the first successful authentication. Subsequent job runs use the last successful credential and then the remaining credentials in the ordered list.

![](/assets/images/D42-25010_nix-AD-job-with-db.png)

### Viewing DB2 discovery job results

3) Once the job finishes, you can view the results of your database discovery. Discovered DB2 databases are added to the On-Prem Databases list page. On the the Device42 main menu, select **Resources -> Databases > On-Prem Databases** to display the page. If you don’t see your DB2 instances, you can search for _DB2_ to narrow down the list.

Click on your DB2 instance name under the _Resource Name_ column to see more information about that database.

![](/assets/images/on-prem-databases-list.png)

## Database Connections Discovery Jobs

Device42 v18.01.00 adds a new Database discovery job type that you can use to discover information about databases in your environment. Database discovery is based on database connection information – including application components and DB server details – you supply for the job. Returned database information is added to the On-Prem Databases list page.

**Note**: If you have already discovered cloud databases using cloud autodiscovery jobs, and you then perform a Database Connections discovery using FQDN, Device42 will not duplicate the databases (which was the previous behavior).

- Select **Discovery > Database** from the main menu to display the Database discovery list page.

![](/assets/images/discovery-database-menu.png)

- Click on a Database discovery _Name_ to view that job. Click **+Add** at the top right of the page to add a new Database discovery job.

![](/assets/images/database-discovery-list.png)

- Click **Edit** at the bottom right of the page to edit an existing job. Click **+Add More** to add additional Database Connection information for the job.

![](/assets/images/D42-25010_db-AD-job-1.png)

- Enter or select the Database Connections information.
    - **Name** – Connection name
    - **Application Component** – Application Component to link to
    - **Platform** – Database type: Oracle, MS SQL, or Postgres
    - **Server** –  IP address of the DB server
    - **Port** – DB server port
    - **Credential** – DB server access credentials
    - **Extra** – DB-type specific: _Instance_ for Oracle, _Database_ for Postgres and DB2
- Use the _Trash_ icon to remove a Connections line.
- Scroll down the page to add or edit the _Discovery Schedule_ for the job.
- Click **Save** to save the job.

### Database Discovery Job Scores

You can view Discovery Scores for Database jobs that have run.

- Select **Analytics > Discovery Status > Discovery Scores** to display the Discovery Scores list page.

![](/assets/images/discovery-scores-menu.png)

- Click on the job you want to see under the _Discovery Target_ column.

![](/assets/images/discovery-target.png)

- Click on **Detailed Discovery Scores** to see additional information.

![](/assets/images/detailed-discovery-scores.png)

### View Discovered Databases

Discovered databases are added to the On-Prem Databases list page.

- Select **Resources > Databases > On-Prem Databases** to display the list page.

![](/assets/images/on-prem-databases-menu.png)

- Click on the name of the database you want to see in the _Resource Name_ column.

![](/assets/images/on-prem-databases-resource-name.png)
