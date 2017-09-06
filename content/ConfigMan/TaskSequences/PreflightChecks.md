---
title: "Preflight Checks"
date: 2017-09-05T15:50:38-06:00
draft: false
weight: 2
---
Preflight checks are great for task sequences where you want to validate whether or not the task sequence should/can run. I handle preflight checks using a combination of options on "Set Dynamic Variables" and a final "Run Command Line" step with an option to look at that "master" variable. I also write to a separate variable to keep track of which pre-flight checks have failed so that I can notify on all the failure rather than possibly running the task sequence multiple times to detect them all.

### The Setup
First create a folder for your pre-flight checks to keep everything organized. This should be the first step in your task sequence (or very early on).

![List of steps in a task sequence](/img/ConfigMan/TaskSequences/PreflightChecks/ts-pfc1.png?classes=border,shadow)

Next you need to create a step that runs when we detect a failure. A "Run Command Line" step that runs `cmd /c echo fail` with options "Success codes:" equal 9999 and only runs when "TSFailure" equals "True"

![Options for Run Command Line Step](/img/ConfigMan/TaskSequences/PreflightChecks/ts-pfc2.png?classes=border,shadow)
![Properties for Run Command Line Step](/img/ConfigMan/TaskSequences/PreflightChecks/ts-pfc3.png?classes=border,shadow)

### Preflight Check Template
The template for a preflight check is just a "Set Dynamic Variables" step. It sets the TSFailure variable to "True" and then adds a description of what the failure was to PFFailure using two if statements (one to add if it already exists, one to create if it doesn't exist).

![Properties for a preflight check](/img/ConfigMan/TaskSequences/PreflightChecks/ts-pfc4.png?classes=border,shadow)

### Known Preflight Checks
This is a list of pre-flight checks that I use on the regular...

#### AC Power Check
This one is relatively simple, except that when you run this step in WinPE you need to load the battery driver. To do that create a "Run Command Line" step before this preflight check that runs the following command:

`drvload %SystemRoot%\Inf\Battery.inf`

And have this step only run if the Task Sequence Variable "_SMSTSInWinPE" equals "True"

The code for this preflight check is as follows:

##### Options

WMI Query: `select * from Win32_Battery where BatteryStatus <> 2`

##### Properties

![Properties for AC Power Check](/img/ConfigMan/TaskSequences/PreflightChecks/ts-pfc6.png?classes=border,shadow)

#### USB Drive Check
This one is really simple too (relatively). Basically just a WMI query to Win32_DiskDrive and make sure that the interface is not USB. I did run into one gotcha with Lenovo machines for a specific card reader (Generic-SD/MMC USB Device) that I needed to exclude.

#### Options

WMI Query: `select * from Win32_DiskDrive WHERE InterfaceType="USB" and not Model="Generic- SD/MMC USB Device"`

#### Properties

![Properties for USB Drive Check](/img/ConfigMan/TaskSequences/PreflightChecks/ts-pfc7.png?classes=border,shadow)

#### Wired Network Check
This one is more challenging. It relies on Win32_NetworkAdapter and the most reliable way to run this one in my opinion is to get the names of the adapters, add the AdapterType property, and add the NetConnectionStatus property. The structure of your query will read

WHERE (

(Name like "%nameofadapter% OR Name like "%nameofadapter2%)

AND AdapterType LIKE "%ethernet%"

AND NetConnectionStatus = 2

)

Clear as mud? I was able to get the names of the network adapters through SSMS "Reports > Hardware - Network Adapters > Count network adapters by type"

#### OPTIONS

WMI Query: 
```
Select * from Win32_NetworkAdapter 
WHERE (
  (Name LIKE "%EthernetAdapterName%" OR Name LIKE "%Ethernet Adapter Name 2%") 
  AND AdapterType LIKE "%ethernet%" 
  AND NetConnectionStatus=2
)
```

#### PROPERTIES

![Properties for Wired Network Check](/img/ConfigMan/TaskSequences/PreflightChecks/ts-pfc8.png?classes=border,shadow)