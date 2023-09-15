---
order: 65
icon: list-unordered
---
# Directory Maintanace {#cap:DIRMAINT}

Before create our first virtual machine (VM), we are going to learn
about DIRMAINT.

## DIRMAINT

IBM Directory Maintenance z/VM (DirMaint) is a Conversational Monitor
System (CMS) application that helps you manage your VM directory.
Directory management is simplified by DirMaint's command interface and
automated facilities. DirMaint's directory statement-like commands are
used to add, delete or alter the directory. DirMaint's error checking
ensures that only valid changes are made to the directory, and that only
authorized personnel are able to make the requested changes. Any
transaction requiring the allocation or deallocation of minidisk extents
can be handled automatically. All user-initiated transactions are
password controlled, and are recorded for auditing purposes.

Features:

::: easylist
& Automated control of the z/VM user directory through a command
interface & Auditing and controls for access to the directory & Error
checking & Automated facilities for minidisk allocation, deallocation,
and copying. & The ability to work in conjunction with external security
managers, such as RACF.
:::

There are individual Dirmaint commands for each directory statement.

The general format of a DirMaint command is:

```
DIRMaint [ prefix ] command [ cmd_options ]
```

Where:

*DIRMaint* is the name of the DirMaint EXEC.

*prefix* is an optional command prefix keyword and any operands required
for that keyword.

*command* is the DirMaint command.

*cmd_options* are any options to be passed to command.

Example:

```
DIRM for linux1 REView
```

The DirMaint command provides the interface between the command issuer
and the DirMaint service machine. The issuer types the DirMaint command
followed by a command operand. The DirMaint command routes the command
to the DirMaint service machine where the service machine does
validation checking, and either processes the request or rejects it with
an appropriate message. The request sent to the service machine may
include prefix keywords.

The DirMaint command may be abbreviated to **DIRM**.

## Extent control

The EXTENT CONTROL file defines any volume that is being used for
minidisk allocation and provides a template, or layout, of how the space
should be used. In addition, it also contains system and device default
values used during allocation operations.

This file can be updated dynamically with the DIRM DASD command.

To check how the EXTENT CONTROL file looks like you can use:

```
===> DIRM SEND EXTENT CONTROL
```

Receive the file and check the content:

```
===> X EXTENT CONTROL
```

You will see something like this file:

```
* ********************************************************************
:REGIONS.
  *RegionId  VolSer    RegStart      RegEnd  Dev-Type  Comments
:END.
:GROUPS.
  *GroupName RegionList
:END.
:SSI_VOLUMES.
  *VolumeFamily     Member   VolSer
:END.
:DEFAULT_GROUPS.
  *GroupName Member
:END.
:EXCLUDE.
  * entry_name Address
  MAINT-*      0122
  MAINT-*      0123
  MAINT-*      0124
  MAINT-*      0125
  MAINT-*      0126
  MAINT630     0131
  MAINT630     0132
  PMAINT       0141
  PMAINT       0142
  SYSDMP-*     0123
:END.
:AUTOBLOCK.
  * IBM supplied defaults are contained in the AUTOBLK DATADVH file.
  * The following are customer overrides and supplements.
  *
  *DASDType BlockSize Blocks/Unit Alloc_Unit Architecture
:END.
:DEFAULTS.
  * IBM supplied defaults are contained in the DEFAULTS DATADVH file.
  * The following are customer overrides and supplements.
  *
  *DASDType Max-Size
:END.
```

Each section has a special function:

::: easylist
& REGIONS: Defines an area or region on your DASD volume for use during
DirMaint automatic allocation. & GROUPS: Defines a grouping of regions
for use during DirMaint automatic allocation. & EXCLUDE: Defines users
or user/device combinations that should be considered as excluded by the
DirMaint DASD subsystem. & AUTOBLOCK: Defines blocking factors and
device architectures for various device types. These supplement or
override the IBM supplied definitions in the AUTOBLK DATADVH file. &
DEFAULTS: Defines the default maximum size for various DASD devices.
These supplement or override the IBM supplied definitions in the DEFAULT
DATADVH file. & END: Defines the ending tag for all sections.
:::

**We are not going to edit this file manually**, we are going to use
DIRMAINT commands to avoid errors and typos. You can close the file
using "QQUIT".

## Adding disks to Extent control

Assuming that you have executed
[2.11.6.2](#sec:formatdisks){reference-type="ref"
reference="sec:formatdisks"}, you have disks 301, 302 and 303 formatted
as PERM. These 3 disks will be used to create our VMs, but first we need
to assign them to DIRMAINT. We are going to create REGIONS (using all
cylinders available) and POOLs of disks. We are going to use the label
of each disk as the name of the REGION, ex: LNX301, LNX302 and LNX303.

The DIRMAINT command that add disks to EXTENT CONTROL is:

```
===> DIRM DASD
```

If you type DIRM DASD logged as maint the following panel will open for
you:

```
 --------------------------------DirMaint DASD--------------------------------
 Add, delete or query DASD statement associated with Group, Region & Volume.
 Select one:   _ Add  _ Delete          _ Query  or _ FREExt  or _ USEDext
 Select one of the following for Add:
  Group  GroupName  ==>           _ (LINEAR) (or) _ (ROTATING) (Optional)
         Region1    ==>           Region2 ==>            Region3 ==>         
  Region RegionName ==>           Volid   ==>        Device Type ==>         
    Optionally you can fill one or all of the following:
     Size =>            Start =>            Comments =>                       
  Volume       Volid =>               Device Type =>         
    Optionally you can fill one or all of the following:
     Size =>            Start =>            Comments =>                       
 Select one of the following for Delete:
  Group  GroupName =>          Region1 =>          Region2  =>          or _ *
  Region Region1   =>          Region2 =>          Region3  =>         
  Volume Volid1    =>          Volid2  =>          Volid3   =>       
 Select one of the following for Query:
  Group  GrpName1 =>          GrpName2 =>          GrpName3 =>          or _ *
  Region Region1  =>          Region2  =>          Region3  =>          or _ *
  Volume Volid1   =>          Volid2   =>          Volid3   =>          or _ *



5741-A07 (c) Copyright IBM Corporation 1979, 2011.
   1= Help     2= Prefix Operands      3= Quit     5=Submit      12=Cursor
```

We will assign DASD 301 (LNX301) to the new region LNX301. The panel
below is an example:

::: bclogo
Device Types You need to ask the instructor what is the device type for
your PERM disks. The example below uses 3390-09, but there are others
like 3390-03 and 3390-27.
:::

```
  --------------------------------DirMaint DASD--------------------------------
  Add, delete or query DASD statement associated with Group, Region & Volume.
  Select one:   (*|\textcolor{red}{X}|*) Add  _ Delete          _ Query  or _ FREExt  or _ USEDext
  Select one of the following for Add:
   Group  GroupName  ==>           _ (LINEAR) (or) _ (ROTATING) (Optional)
          Region1    ==>           Region2 ==>            Region3 ==>         
   Region RegionName ==> (*|\textcolor{red}{LNX301}|*)    Volid   ==> (*|\textcolor{red}{LNX301}|*) Device Type ==> (*|\textcolor{red}{3390-09}|*)  
     Optionally you can fill one or all of the following:
      Size => (*|\textcolor{red}{END}|*)       Start => (*|\textcolor{red}{1}|*)          Comments => (*|\textcolor{red}{linux1 vol}|*)            
   Volume       Volid =>               Device Type =>         
     Optionally you can fill one or all of the following:
      Size =>            Start =>            Comments =>                       
  Select one of the following for Delete:
   Group  GroupName =>          Region1 =>          Region2  =>          or _ *
   Region Region1   =>          Region2 =>          Region3  =>         
   Volume Volid1    =>          Volid2  =>          Volid3   =>       
  Select one of the following for Query:
   Group  GrpName1 =>          GrpName2 =>          GrpName3 =>          or _ *
   Region Region1  =>          Region2  =>          Region3  =>          or _ *
   Volume Volid1   =>          Volid2   =>          Volid3   =>          or _ *




 5741-A07 (c) Copyright IBM Corporation 1979, 2011.
    1= Help     2= Prefix Operands      3= Quit     (*|\textcolor{red}{5=Submit}|*)      12=Cursor
```

When you get it filled as needed press **F5** to submit your request or
F3 to cancel it.

The same result above could be accomplished using the following command
line (to avoid the panel):

```
===> DIRM DASD ADD REGION LNX301 LNX301 3390-09 END 1
```

To apply the changes to EXTENT CONTROL:

```
===> DIRM RLDE
```

If you need to query information about a specific region you can
execute:

```
===> DIRM DASD QUERY REGION LNX301
```

It will provide, depending the disk type, the following output:

```
DIRM DASD QUERY REGION LNX301
DVHXMT1191I Your DASD request has been sent for processing to DIRMAINT
DVHXMT1191I at ZVMWSXX.
Ready; T=0.01/0.01 13:08:35
 DVHREQ2288I Your DASD request for MAINT at * has been accepted.
 DVHDSD3563I REGION   VOLUME    START      SIZE        END    DEV-TYPE
 DVHDSD3563I COMMENTS
 DVHDSD3564I LNX301   LNX301          1       10016       10016 3390-09
 DVHREQ2289I Your DASD request for MAINT at * has completed; with RC = 0.
```

If the REGION is not valid, the following output will appear:

```
DIRM DASD QUERY REGION LNX301
DVHXMT1191I Your DASD request has been sent for processing to DIRMAINT
DVHXMT1191I at ZVMWSXX.
Ready; T=0.01/0.01 13:02:04
 DVHREQ2288I Your DASD request for MAINT at * has been accepted.
 DVHDSD3558W Region LNX301 cannot be queried, there is no region by
 DVHDSD3558W that name.
 DVHREQ2289I Your DASD request for MAINT at * has completed; with RC = 0.
```

### Exercise {#exercise}

::: easylist
& Add LNX302 and LNX303 to EXTENT CONTROL using DIRM DASD command. &&
Add LNX302 using the panel && Add LNX303 using the command line && Check
both using DIRM DASD QUERY command. && Apply the changes using DIRM RLDE
:::

## Creating a pool of disks

We have assigned LNX301, LNX302 and LNX303 to DIRMAINT. This setup allow
us to assing disks to a VM using the REGION name, but we didn't group
them yet. These groups of disks are called POOLs, and it is another
abstraction that can be used to assign disks to a VM.

Using the same DIRM DASD panel we can create our new POOL, lets call it
"LINUX":

```
  --------------------------------DirMaint DASD--------------------------------
  Add, delete or query DASD statement associated with Group, Region & Volume.
  Select one:   (*|\textcolor{red}{x}|*) Add  _ Delete          _ Query  or _ FREExt  or _ USEDext
  Select one of the following for Add:
   Group  GroupName  ==> (*|\textcolor{red}{LINUX}|*)     (*|\textcolor{red}{X}|*) (LINEAR) (or) _ (ROTATING) (Optional)
          Region1    ==> (*|\textcolor{red}{LNX301}|*)    Region2 ==> (*|\textcolor{red}{LNX302}|*)     Region3 ==> (*|\textcolor{red}{LNX303}|*)  
   Region RegionName ==>           Volid   ==>        Device Type ==>         
     Optionally you can fill one or all of the following:
      Size =>            Start =>            Comments =>                       
   Volume       Volid =>               Device Type =>         
     Optionally you can fill one or all of the following:
      Size =>            Start =>            Comments =>                       
  Select one of the following for Delete:
   Group  GroupName =>          Region1 =>          Region2  =>          or _ *
   Region Region1   =>          Region2 =>          Region3  =>         
   Volume Volid1    =>          Volid2  =>          Volid3   =>       
  Select one of the following for Query:
   Group  GrpName1 =>          GrpName2 =>          GrpName3 =>          or _ *
   Region Region1  =>          Region2  =>          Region3  =>          or _ *
   Volume Volid1   =>          Volid2   =>          Volid3   =>          or _ *




 5741-A07 (c) Copyright IBM Corporation 1979, 2011.
    1= Help     2= Prefix Operands      3= Quit     (*|\textcolor{red}{5=Submit}|*)      12=Cursor
```

When you get it filled as needed press **F5** to submit your request or
F3 to cancel it.

The same result above could be accomplished using the following command
line (to avoid the panel):

```
DIRM DASD ADD GROUP LINUX LNX301 LNX302 LNX303
```

To apply the changes to EXTENT CONTROL:

```
===> DIRM RLDE
```

You can query your new group information using the following commad:

```
DIRM DASD QUERY GROUP LINUX
```

It will provide the following output:

```
DIRM DASD QUERY GROUP LINUX
DVHXMT1191I Your DASD request has been sent for processing to DIRMAINT
DVHXMT1191I at ZVMWSXX.
Ready; T=0.01/0.01 14:13:19
 DVHREQ2288I Your DASD request for MAINT at * has been accepted.
 DVHDSD3561I GROUP=LINUX ALLOCATE=(LINEAR) REGIONS=3
 DVHDSD3562I GROUP=LINUX REGIONS=LNX301   LNX302   LNX303
 DVHREQ2289I Your DASD request for MAINT at * has completed; with RC = 0.
```

![Our Environment with
DIRMAINT](/imgs/workshop-dirmaint.png){#fig:workshopdirmaint}
