---
order: 85
icon: ":gear:"
---
# System Configuration {#ch:systemconfig}

As a system programmer, you should become familiar with the SYSTEM
CONFIG file.

The SYSTEM CONFIG file contains the primary system definitions used when
CP is booted (IPLed). All of the information needed to configure CP
statically comes from this file. In an SSI cluster, all members use the
same SYSTEM CONFIG file; however, you can specify that certain
configuration statements apply only to specific members by qualifying
the statements with a system identifier. This topic has examples of this
specifying method.

The SYSTEM CONFIG file resides on a special CMS-formatted minidisk (CF0)
belonging to the PMAINT user ID. Minidisks containing such objects are
called parm disks because when allocated those disks are given a special
record category type called "PARM". There can be more than one parm disk
allocated in a z/VM system for backup and recovery.

Related information: "Using Configuration Files" in z/VM: CP Planning
and Administration, SC24-6178-09.

## Exercises

### System parameter disks

The SYSTEM CONFIG file is located on a system's parameter disk. Before
you begin updating the SYSTEM CONFIG file, you must access the parm disk
(In this case, the PMAINT's CF0).

MAINT's CF1, and CF3 minidisks are traditionally used as the system's
parameter disks. Use the QUERY CPDISK command to query what system
parameter disks are in use with your system:

::: easylist
& Logon as MAINT & Enter the following command and press Enter
:::

```
===> q cpdisk
```

```
q userid                                                             
MAINT    AT ZVMWSXX                                                  
q cpdisk                                                             
Label  Userid   Vdev Mode Stat Vol-ID Rdev Type   StartLoc     EndLoc
MNTCF1 MAINT    0CF1  A   R/O  AB1RES 6205 CKD          39        158
MNTCF3 MAINT    0CF3  C   R/O  AB1RES 6205 CKD         160        279
```

### Steps for accessing the parm disk and creating a backup for SYSTEM CONFIG

Perform these steps to access the CF0 parm disk:

::: easylist
& Logon as MAINT & Access the parm disk (PMAINT's CF0). Type these
commands and press the Enter key after each command:

```
===> link pmaint cf0 cf0 mr

===> access cf0 x
```

```
q userid                                                                        
MAINT    AT ZVMWSXX                                                             
Ready; T=0.01/0.01 08:30:09                                                     

link pmaint cf0 cf0 mr                                                          
Ready; T=0.01/0.01 08:30:23                                                     

access cf0 x                                                                    
Ready; T=0.01/0.01 08:30:37                                                     

q disk                                                                          
LABEL  VDEV M  STAT   CYL TYPE BLKSZ   FILES  BLKS USED-(%) BLKS LEFT  BLK TOTAL
MNT191 191  A   R/W   175 3390 4096        4         11-01      31489      31500
MNT5E5 5E5  B   R/O    18 3390 4096      134       1537-47       1703       3240
MNT2CC 2CC  C   R/O    10 3390 4096        3        104-06       1696       1800
MNT51D 51D  D   R/O    26 3390 4096      293       1796-38       2884       4680
PMT551 551  E   R/O    40 3390 4096        8        119-02       7081       7200
MNT190 190  S   R/O   207 3390 4096      696      18085-49      19175      37260
MNT19E 19E  Y/S R/O   500 3390 4096     1123      30403-34      59597      90000
(*|\textcolor{red}{PMTCF0 CF0}|*)  X   R/W   120 3390 4096        2         15-01      21585      21600
Ready; T=0.01/0.01 08:30:39                                                     
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                            RUNNING   ZVMWSXX   
```

& As a preferred practice, create a backup of SYSTEM CONFIG:

```
===> copy system config x system configbk x 
```

```
copy system config x system configbk x  
Ready; T=0.01/0.01 08:32:53             

listfile * * x                          
LOGO     CONFIG   X1                    
SYSTEM   CONFIG   X1                    
SYSTEM   CONFIGBK X1                    
Ready; T=0.01/0.01 08:32:59      
```
:::

You know you are done when you have access to the CF0 disk and have
created a backup of SYSTEM CONFIG on the parm disk.

### Edit the SYSTEM CONFIG file

After you linked in "Multiple Write Mode", you can edit the SYSTEM
CONFIG file with XEDIT editor.

Before you begin updating the SYSTEM CONFIG file, we will create a small
REXX that will automate the access to SYSTEM CONFIG disk. From MAINT use
XEDIT to create a new file called GETCF0 EXEC A:

```
===> xedit getcf0 exec a
```

Add the follow content:

```
/* REXX will link PMAINT CF0 */

'LINK PMAINT CF0 CF0 MR'
'ACCESS CF0 X'
```

Execute a script "GETCF0" which will access the parm disk as the "X"
disk.

### Steps for updating the CP-owned volume list

The CP-owned volume list is the place where you specify the labels of
paging, spooling, and temporary disk volumes that CP should
automatically attach to the system during IPL. These volumes contain the
system data for your z/VM system. All other volumes on the system are
considered user volumes.

**Before you begin:** You need to format and allocate your paging,
spooling, temporary disk or Perm Volumes. You need to access to the parm
disk.

Perform these steps to update the CP-owned volume list:

::: easylist
& Logon as MAINT & Edit the SYSTEM CONFIG file. Type any of these two
commands to get access and edit the file, and press the Enter key:

```
===> xedit system config x
```

Or

```
===> filel * * x (go to the CMD column of the file, type x and press enter)
```

You will see a file like the following:

```
SYSTEM   CONFIG   X1  F 80  Trunc=80 Size=376 Line=0 Col=1 Alt=0               
                                                                                
00000 * * * Top of File * * *                                                   
00001 /**********************************************************************/  
00002 /*             SYSTEM CONFIG FILE                                     */  
00003 /**********************************************************************/  
00004 /*                                                                    */  
00005 /*  Refer to CP Planning and Administration for SYSTEM CONFIG rules   */  
00006 /*                                                                    */  
00007 /*  Warning - Always run CPSYNTAX after updating the SYSTEM CONFIG    */  
00008 /*                                                                    */  
00009 /**********************************************************************/  
00010                                                                           
00011                                                                           
00012 /**********************************************************************/  
00013 /*                   System_Identifier Information                    */  
00014 /**********************************************************************/  
00015                                                                           
00016   /* System_Identifier LPAR ZVMWSXX  ZVMWSXX */                           
00017   /* System_Identifier LPAR @@LU-2 @@MEMSLOT2 */
00018   /* System_Identifier LPAR @@LU-3 @@MEMSLOT3 */                          
00019   /* System_Identifier LPAR @@LU-4 @@MEMSLOT4 */                          
00020                                                                           
00021       System_Identifier * * ZVMWSXX                                       
00022                                                                           
00023                                                                           
00024 /**********************************************************************/  
00025 /*         SSI Statement required for VMSSI feature                   */  
00026 /**********************************************************************/  
00027                                                                           
00028   SSI CLUSTER1  PDR_VOLUME VMCOM1,                                        
00029         SLOT 1 ZVMWSXX                                                    
00030 /*      SLOT 2 @@MEMSLOT2,  */                                            
00031 /*      SLOT 3 @@MEMSLOT3,  */                                            
00032 /*      SLOT 4 @@MEMSLOT4   */                                            
00033                                                                           
00034                                                                           
00035 /**********************************************************************/  
00036 /*                Checkpoint and Warmstart Information                */  
00037 /**********************************************************************/  
00038                                                                           
00039   ZVMWSXX:     System_Residence,                                          
====>                                                                           
```

& Find the string "DUMP & SPOOL VOLUMES". At the XEDIT command line,
type this command and press the Enter key:

```
===> /dump/ & /spool/ & /volume/
```

& Add additional Spool volumes to slots in **ascending** order (slots
11, 12, and so forth).

Duplicate the following line:

::: bclogo
Duplicating a line on XEDIT To duplicate a line in XEDIT place a double
quote (\") in the line number and press enter, example:

00080
/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/

00081

[\"]{style="color: red"}0082 CP_Owned Slot 10 M01S01

00083

00084
/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/
:::

```
00082     CP_Owned   Slot  10  M01S01
```

Add the Spool volume previously formatted (M01S02) **below the M01S01
line**.

You should now see something like the following:

```
00072 /*                                           DUMP & SPOOL VOLUMES     */
00073 /* Dump and spool volumes begin with slot 10 and are assigned in      */
00074 /* ascending order, without regard to the system that owns them.      */
00075 /* In an SSI cluster all member systems MUST have access to ALL spool */
00076 /* volumes and they MUST be assigned the same slot number on each     */
00077 /* system. When moving to an SSI cluster volume ownership, which      */
00078 /* determines which member owns and can allocate the spool space,     */
00079 /* will be established by CPFMTXA.                                    */
00080 /**********************************************************************/
00081
00082     CP_Owned   Slot  10  M01S01
00083     CP_Owned   (*|\textcolor{red}{Slot  11}|*)  (*|\textcolor{red}{M01S02}|*)
```

& To add the PAGE volume, find the string "PAGE & TDISK VOLUMES" (Tip:
you may be able to see this section just below the SPOOL section). At
the XEDIT command line, type this command and press the Enter key:

```
===> /page/ & /tdisk/ & /volume/
```

& Add additional Page volumes to slots in **descending** order (slots
254, 253, and so forth).

Duplicate the following line:

```
00095     CP_Owned   Slot 255  M01P01
```

Add the Page volume previously formatted (M01P02) **above the M01P01
line**.

You should now see something like the following:

```
SYSTEM   CONFIG   Z1  F 80  Trunc=80 Size=280 Line=85 Col=1 Alt=2              
                                                                                
00085 /*                                           PAGE & TDISK VOLUMES     */  
00086 /* In a SSI cluster Paging and T-disk volumes are not shared.         */  
00087 /* To avoid interference with spool volumes and to automatically      */  
00088 /* have all unused slots defined as "Reserved", begin with slot 255   */  
00089 /* and assign them in descending order.  It is NOT required that each */  
00090 /* system use the same slot numbers for paging and T-disk volumes.    */  
00091 /* When moving to an SSI cluster volume ownership will be established */  
00092 /* by CPFMTXA OWNER.                                                  */  
00093 /**********************************************************************/  
00094                                                                           
00095     CP_Owned   (*|\textcolor{red}{Slot 254}|*)   (*| \textcolor{red}{M01P02} |*)                                           
00096     CP_Owned   Slot 255  M01P01                                           
00097                                                                           
00098 /**********************************************************************/  
00099 /*                          User_Volume_List                          */  
00100 /**********************************************************************/  
00101 /* These volumes contain the minidisks for your guests, as well as    */  
00102 /* the product disks for z/VM. Volumes that are not intended to hold  */  
00103 /* "local" minidisks, i.e., minidisks that would be unique to a       */  
00104 /* single system, should be kept on separate volumes.                 */  
00105                                                                           
00106 /**********************************************************************/  
00107 /* Shared User Volumes                                                */  
00108 /**********************************************************************/  
00109                User_Volume_List  VMCOM2 630RL1 630RL2                     
00110                                                                           
00111 /**********************************************************************/  
00112 /* User volumes for local minidisks                                   */  
00113 /**********************************************************************/  
====>                         
```

& Save the file. At the XEDIT command line, type this command and press
the Enter key:

```
===> save
```
:::

### Steps for updating the user volume list

Just as there is a list of DASD volumes that CP should automatically
attach to the system during IPL for access to CP system areas, there is
a list of DASD volumes that CP should automatically attach to the system
for user minidisk definitions.

Because all minidisks are managed by CP, all volumes that house
minidisks must be attached to the z/VM system. CP must control the
volumes so it can reorient channel programs initiated by a guest
operating system. The guest perceives its disks as starting at cylinder
0, but the true location of the guest's minidisk starts at an offset of
real cylinder 0.

Unless you qualify user volume statements with a system name, the user
volumes are attached to all members in the SSI cluster.

If no user volumes are attached to the system at IPL time, the real
devices housing minidisks need to be attached **manually**. Otherwise,
virtual machines will have no disks. To avoid manual attachment, you can
tell CP to look for DASD volume labels and attach those devices at IPL
time.

The **USER_VOLUME_LIST** statement directs CP to attach specific user
DASD volumes at z/VM load (IPL) time. The **USER_VOLUME_INCLUDE**
statement allows you to create a general volume identifier and to
include all volumes that match the general identifier.

Example: If all your Linux user volumes have a volume identifier
starting with LNX, you can add this statement:

```
02109          User_Volume_Include LNX*
```

If a volume is normally attached to the system using a
USER_VOLUME_INCLUDE statement, CP does not notify the operator if the
volume is not mounted. If a user volume is necessary for normal system
operation, specify it with a USER_VOLUME_LIST statement so that the
operator is notified during system initialization if the volume is not
mounted.

**Before you begin:** You need to format and allocate the user volumes
you need. You need to access to the parm disk.

::: easylist
& If not already doing so, you must edit the SYSTEM CONFIG file. Type
this command and press the Enter key:

```
===> xedit system config x
```

Perform these steps to update the user volume list:

& Find the string "User_Volume_List" in the section titled "User volumes
for local minidisks." At the XEDIT command line, type this command and
press the Enter key:

```
===> /user_volume_list
```

& For shared user volumes, update the User_Volume_List statements in the
"Shared User Volumes" section.

& Include the LNX301, LNX302 and LNX303 volumes on this list (We will
use a wildcard LNX\*)

& In the prefix area for the last USER_VOLUME_LIST statement, type "i2"
and press the Enter key. For instance:

```
00099 /*                          User_Volume_List                          */
00100 /**********************************************************************/
00101 /* These volumes contain the minidisks for your guests, as well as    */
00102 /* the product disks for z/VM. Volumes that are not intended to hold  */
00103 /* "local" minidisks, i.e., minidisks that would be unique to a       */
00104 /* single system, should be kept on separate volumes.                 */
00105                                                                         
00106 /**********************************************************************/
00107 /* Shared User Volumes                                                */
00108 /**********************************************************************/
(*|\textcolor{red}{i2}|*)109                User_Volume_List  VMCOM2 630RL1 630RL2                   
00110                                                                         
00111 /**********************************************************************/
00112 /* User volumes for local minidisks                                   */
00113 /**********************************************************************/
```

& Add a User_Volume_Include statement. If you followed the instructions
from the last chapter you probably have volumes named LNX301, LNX302 and
LNX303, so it makes sense to use LNX\* wildcard for our volumes.
Example:

```
00099 /*                          User_Volume_List                          */
00100 /**********************************************************************/
00101 /* These volumes contain the minidisks for your guests, as well as    */
00102 /* the product disks for z/VM. Volumes that are not intended to hold  */
00103 /* "local" minidisks, i.e., minidisks that would be unique to a       */
00104 /* single system, should be kept on separate volumes.                 */
00105                                                                         
00106 /**********************************************************************/
00107 /* Shared User Volumes                                                */
00108 /**********************************************************************/
00109                User_Volume_List  VMCOM2 630RL1 630RL2                   
00110                (*|\textcolor{red}{User\_Volume\_Include  LNX*}|*)                                                                        
00111                                                                       
00112 /**********************************************************************/
00113 /* User volumes for local minidisks                                   */
00114 /**********************************************************************/
```

& When you finish the User_Volume_List statements, press the Enter key.
& Save the file. At the XEDIT command line, type this command and press
the Enter key:

```
===> save
```

Question: Is there another way to add those LNX\* disks to the User
Volume List?

R:
:::

### Steps for updating the FEATURES statement

The FEATURES statement in SYSTEM CONFIG allows you to modify attributes
associated with the running system at IPL time. In this procedure, you
will modify some of the features.

::: easylist
& The **Auto_Warm_IPL** feature causes CP to bypass prompting for start
options, provided the previous system shutdown was successful. The
feature allows for a fully automated startup of z/VM.

& The **Clear_TDisk** feature causes CP to erase temporary disks fully
(that is, overwrite the entire temporary disk with zeros) after those
disks are detached. The feature prevents another user who might define
an identically sized temporary disk from accessing data written by the
previous user.

& The **Retrieve** defines the default and maximum number of retrieve
buffers allowed per user on your system. Retrieve buffers create a
command history, from which users can retrieve commands previously
issued. Command retrieval is usually assigned to a program function key
such as PF12 (F12). The assignment is through the CP SET command, SET
PF12 RETRIEVE. By pressing PF12, a command is retrieved and written back
into the command area on the terminal screen. You probably do not need
to change these settings.

& The **Passwords_on_Cmds** feature allows users to use passwords when
using the CP AUTOLOG, LINK, or LOGON commands.

& The **Disconnect_timeout** feature controls whether and when a virtual
machine is logged off after it has been forced to disconnect. You will
turn this feature off, so that any virtual machine that has been forced
to disconnect will not be logged off.

& The **ShutdownTime** and **Signal ShutdownTime** features enable a
virtual machine to register with CP to receive a shutdown signal when
z/VM is shutting down. CP waits to shut itself down until the time
interval (in seconds) is exceeded, or all of the virtual machines
enabled for the signal shutdown have reported a successful shutdown.
Some Linux distributions support this function, which allows Linux to
shut down cleanly before z/VM shuts down. []{#sec:signalshutdown
label="sec:signalshutdown"}

**Note:** Unless you qualify the features with a system name, these
features will be the same for all members in your SSI cluster.
:::

Procedure: Find the line containing the text "Features Statement", it is
a section. At the XEDIT command line, type this command and press the
Enter key:

```
===> /Features Statement
```

You will see something like this:

```
00146 /*                         Features Statement                         */
00147 /**********************************************************************/
00148
00149  Features ,
00150    Disable ,                       /* Disable the following features */
00151      Set_Privclass ,               /* Disallow SET PRIVCLASS command */
00152      Auto_Warm_IPL ,               /* Prompt at IPL always           */
00153      Clear_TDisk   ,               /* Don't clear TDisks at IPL time */
00154    Retrieve ,                      /* Retrieve options               */
00155      Default  20 ,                 /* Default.... default is 20      */
00156      Maximum  255 ,                /* Maximum.... default is 255     */
00157    MaxUsers noLimit ,              /* No limit on number of users    */
00158    Passwords_on_Cmds ,             /* What commands allow passwords? */
00159      Autolog  yes ,                /* ... AUTOLOG does               */
00160      Link     yes ,                /* ... LINK does                  */
00161      Logon    yes ,                /* ... and LOGON does, too        */
00162    Vdisk Userlim 144000 blocks     /* Maximum vdisk allowed per user */
```

and we will modify to something like this:

```
00148 /*                         Features Statement                         */
00149 /**********************************************************************/
00150
00151  Features ,
00152    Enable,                         /* Enable the following features  */
00153      Set_Privclass ,               /* Allow SET PRIVCLASS command    */
00154      Auto_Warm_IPL ,               /* Do not prompt at IPL           */
00155      Clear_TDisk   ,               /* Clear TDisks at IPL time       */
00156    Retrieve ,                      /* Retrieve options               */
00157      Default  99 ,                 /* Default.... default is 20      */
00158      Maximum  255 ,                /* Maximum.... default is 255     */
00159    MaxUsers noLimit ,              /* No limit on number of users    */
00160    Passwords_on_Cmds ,             /* What commands allow passwords? */
00161      Autolog  yes ,                /* ... AUTOLOG does               */
00162      Link     yes ,                /* ... LINK does                  */
00163      Logon    yes ,                /* ... and LOGON does, too        */
00164    Vdisk ,                         /* Maximum vdisk allowed per user */
00165      Syslim infinite ,
00166      Userlim infinite ,
00167    Disconnect_timeout off
00168    Set ,
00169      ShutdownTime 30 ,
00170      Signal ShutdownTime 500
00171

```

On the next section we will setup the devices to start up online.

### Set up control access to devices at startup

Sometimes your z/VM system might have access to devices that you do not
want to be varied online during IPL. For instance, the devices might
duplicate labels of devices used by your production system, or might be
in use by other LPARs or systems. You can specify ranges of devices that
z/VM should not vary online during IPL.

Find the section titled "Status of Devices". At the XEDIT command line,
type this command and press the Enter key:

```
===> /status of devices
```

On the line with the string "Devices", type i in the prefix area and
press the Enter key:

```
SYSTEM   CONFIG   Z1  F 80  Trunc=80 Size=286 Line=190 Col=1 Alt=2           
                                                                              
00190 /*                        Status of Devices                           */
00191 /**********************************************************************/
00192                                                                         
(*| \textcolor{red}{i} |*)0193  Devices ,                                                              
00194    Online_at_IPL   0000-FFFF,                                           
00195    Sensed          0000-FFFF                                            
00196                                                                         
00197 /**********************************************************************/
00198 /*                     Console Definitions                            */
00199 /**********************************************************************/
00200      
```

Type the following line, then press the Enter key:

```
===> Offline_at_IPL 0000-FFFF,
```

Type over the device range on the ONLINE_AT_IPL statement with a device
range specific for your system.

```
===> Online_at_IPL 0009,   /* Console device */
```

Add a new ONLINE_AT_IPL statement for each device range. Use the i
prefix command to add lines.

```
===> Online_at_IPL 0200-0700,  /* PERM,PAGE,SPOOL */
===> Online_at_IPL 0800-0808,  /* OSAs */
```

The result:

```
SYSTEM   CONFIG   Z1  F 80  Trunc=80 Size=289 Line=187 Col=1 Alt=0           
                                                                              
00187 /**********************************************************************/
00188 /*                        Status of Devices                           */
00189 /**********************************************************************/
00190                                                                         
00191  Devices ,                                                              
00192    Offline_at_IPL   0000-FFFF,                                          
00193    Online_at_IPL    0009,       /* Console */                
00196    Online_at_IPL    0200-0700,  /* PERM,PAGE,SPOOL */
00198    Online_at_IPL    0800-0808,  /* OSAs */
00200    Sensed           0000-FFFF                                           
00201                                                                                            
```

### Set addresses for consoles

During the first IPL of your z/VM system, you needed to specify a load
parameter so you could communicate with the Stand-Alone Program Loader
(SAPL). The reason is the new z/VM system did not know which device
addresses to use to display messages and prompts. The installation
system includes default device addresses for use as the system operator
console and emergency messages console, but these addresses rarely
correspond to your production hardware configuration. So you will not
need to use the SAPL each time you IPL z/VM, you need to supply the
address of your IPL console and your emergency messages console on the
Operator_Consoles statement. During IPL, CP tries each device on the
Operator_Consoles statement (from left to right) until it finds an
active device. If no devices on the list are active, CP loads a disabled
wait state and terminates. The emergency message console is used as an
additional console during failures. Define the emergency console with
the Emergency_Message_Console statement.

Find the section titled "Console Definitions".

```
===> /console 
```

Set **0009** as the first device on the **Operator_Consoles** and the
**Emergency_Message_Consoles** statement. This will tell z/VM to open
operator communications with device **0009** which is the device address
of the virtual console. This will alleviate the need for passing load
parameters at the next boot.

```
00200 /**********************************************************************/
00201 /*                     Console Definitions                            */
00202 /**********************************************************************/
00203                                                                         
00204  Operator_Consoles     (*|\textcolor{red}{0009}|*) 0020 0021 0022 0023 0E20 0E21 1020 ,        
00205                        System_3270 System_Console                       
00206  Emergency_Message_Consoles   (*|\textcolor{red}{0009}|*) 0020 0021 0022 0023 0E20 0E21 1020 , 
00207                               System_Console                            
00208                                                                         
```

Save all the changes you have made so far and exit the SYSTEM CONFIG
file.

```
===> file
```

### Steps for checking the syntax of the SYSTEM CONFIG file

Since the SYSTEM CONFIG file contains very important data, extreme care
must be taken to ensure its contents are correct. The system may not
start correctly if this file contains errors. Fixing the errors can be
cumbersome. z/VM provides a utility, CPSYNTAX, in the 193 minidisk to
check the syntax of the SYSTEM CONFIG file.

**Procedure**

::: easylist
& Logon as MAINT & To gain access to the CPSYNTAX command, access the
193 disk as F. From the command line, type the following command and
press the Enter key:

```
===> access 193 f
```

& Run the CPSYNTAX command. From the command line, type this command and
press the Enter key:

```
===> cpsyntax system config x
```

& Check for a zero return code. If you do not get a zero return code,
modify the SYSTEM CONFIG file and rerun the CPSYNTAX utility. Example of
a non-zero return code:

```
acc 193 f                                                                       
Ready; T=0.01/0.01 17:14:17                                                     

listfile cpsyntax * *                                                           
CPSYNTAX EXEC     F2                                                            
Ready; T=0.01/0.01 17:14:28                                                     

cpsyntax system config x                                                        
HCPSYK6700E FILE SYSTEM CONFIG X, RECORD 164:                                   
HCPSYK6701E INVALID SYSTEM CONFIGURATION FILE STATEMENT - DISCONNECT_TIMEOUT    
                                                                                
Ready(00008); T=0.37/0.38 17:14:46                                              
                                                                                
                                                                                
                                                                                
                                                            RUNNING   ZVMWSXX  
```

& Lets create a FREECF0 EXEC on your MAINT "A-disk", it will release the
cf0 disk for us on future updates:

```
===> x freecf0 exec
```

```
00000 * * * Top of File * * *
00001 /* REXX */             
00002 'RELEASE X'            
00003 'CP DETACH CF0'        
00004 * * * End of File * * *
```

& Execute the script "FREECF0" to release and detach the parm disk CF0.

```
===> freecf0
```
:::

### Restart z/VM and verify changes

In this exercise, you will learn to use some new CP/CMS commands to
verify the changes you have made to the system.

::: easylist
& Shutdown and IPL your z/VM

```
===> shutdown reipl
```

When the system shuts down and re-IPLs, you will see a number of IPL
messages. z/VM restores the system to the same state as it was prior to
shutdown (for instance, with OPERATOR disconnected).

Press the Pause key if the console status is "MORE\..."

To get a z/VM logo, press the Enter key.

Log on as MAINT (password WD5JU8QP)

Press the Enter Key to go from "VM READ" to "RUNNING" State.

& Check paging and spooling space

Use the QUERY ALLOC to display the number of cylinders or pages that are
allocated, in use, and available for DASD volumes attached to the
system.

Display the allocation information for paging space.

```
===> query alloc page
```

The output should show that the system now have two paging volumes.

```
q alloc page                                                
                EXTENT     EXTENT  TOTAL  PAGES   HIGH    % 
VOLID  RDEV      START        END  PAGES IN USE   PAGE USED 
------ ---- ---------- ---------- ------ ------ ------ ---- 
M01P02 0300          0       2500 450180     40     51   1% 
M01P01 0206          1       3337 600660      0      0   0% 
                                  ------ ------        ---- 
SUMMARY                            1026K     24          1% 
USABLE                             1026K     24          1% 
Ready; T=0.01/0.01 11:14:41                                                              
```

Display the allocation information for spooling space.

```
===> query alloc spool
```

If you added another disk for spool, the output should show that the
system now have two spool volumes.

```
query alloc spool                                          
                EXTENT     EXTENT  TOTAL  PAGES   HIGH    %
VOLID  RDEV      START        END  PAGES IN USE   PAGE USED
------ ---- ---------- ---------- ------ ------ ------ ----
M01S01 0205          1       3337 600660   9020  39600   1%
M01P02 0304          1       3337 600660      0      0   0%
                                  ------ ------        ----
SUMMARY                            1026K   9020          1%
USABLE                             1026K   9020          1%
Ready; T=0.01/0.01 11:15:20   
```

& Check the system identifier Use the IDENTIFY command to display
information about your user ID and node

```
===> id
```

```
identify                                                           
MAINT    AT ZVMWSXX  VIA *        09/13/13 11:18:27 EDT      FRIDAY
Ready; T=0.01/0.01 11:18:27  
```

Your system identifier should be ZVMWSxx. The system identifier is also
displayed at the lower right hand corner of your console.

& Check the user volume list

Use the QUERY DASD command to display a list of the DASDs that are
attached to the system.

```
q dasd                         
DASD 0200 CP OWNED  M01RES   89
DASD 0201 CP OWNED  VMCOM1   13
DASD 0202 CP SYSTEM VMCOM2   2 
DASD 0203 CP SYSTEM 630RL1   12
DASD 0204 CP SYSTEM 630RL2   6 
DASD 0205 CP OWNED  M01S01   1 
DASD 0206 CP OWNED  M01P01   0 
DASD 0207 CP SYSTEM M01W01   10
DASD 0208 CP SYSTEM M01W02   1 
DASD 0209 CP SYSTEM M01W03   1 
DASD (*|\textcolor{red}{0300}|*) CP OWNED  (*|\textcolor{red}{M01P02}|*)   0 
DASD (*|\textcolor{red}{0301}|*) CP SYSTEM (*|\textcolor{red}{LNX301}|*)   0
DASD (*|\textcolor{red}{0302}|*) CP SYSTEM (*|\textcolor{red}{LNX302}|*)   0
DASD (*|\textcolor{red}{0303}|*) CP SYSTEM (*|\textcolor{red}{LNX303}|*)   0
DASD (*|\textcolor{red}{0304}|*) CP OWNED  (*|\textcolor{red}{M01S02}|*)   0 
Ready; T=0.01/0.01 11:19:21    
```

& Check features Use the QUERY RETRIEVE command to display the setting
of the retrieve key buffer limits.

```
===> q ret
```

```
q ret                                                     
99 buffers available.  Maximum of 255 buffers may be selected.
Ready; T=0.01/0.01 12:27:53      
```

A maximum of 99 commands can now be stored and retrieved from the
retrieve key buffer. The retrieve keys (PF11 and PF12) are defined in
the PROFILE EXEC file.

& Check offline devices

Once again, use the QUERY DASD command to display any offline devices.

```
===> query dasd offline
```

```
query dasd offline             
An offline DASD was not found. 
Ready; T=0.01/0.01 12:29:29
```
:::

This is our actual environment after the changes:

![Environment with extra PAGE and
SPOOL](/imgs/workshop-pagespool.png){#fig:workshoppagespool}
