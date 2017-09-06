---
title: "SQL Server"
date: 2017-09-05T23:34:51-06:00
draft: false
weight: 2
---
This page covers some of the prerequisites and gotchas when it comes to the
SQL configuration.

### SQL Version Chart
Central Site: Standard or Enterprise  
Primary Site: Standard or Enterprise  
Secondary Site: Express

More configuration info [here](https://docs.microsoft.com/en-us/sccm/core/plan-design/configs/support-for-sql-server-versions)

### SQL Service Accounts
You'll need at least one domain account to run services from. You can use
multiple, and honestly it's probably more up to your SQL administrator than it 
will be up to you.

### Remote vs Local
Okay look... both work... both are supported... but one does work better. Do a
local install wherever possible.

### Server Manager Prerequisites
.NET Framework 3.5

### SQL Features to install
Database Engine services  
Reporting Services - Native  
Management Tools (optional, but recommended)

### SQL Memory
By default SQL will use as much memory as it possibly can. You should mitigate
this by configuring a limit. You can do this in SSMS.