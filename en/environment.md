---
order: 96
icon: ":rocket:"
---

# Workshop Environment

## Hardware

The hardware configuration depends on where this workshop is running.
The instructor will cover it using the presentation.

## Your Virtual Machine

![Second level z/VM at the beginning of the
workshop](/imgs/workshop1.png){#fig:zvm2nd}


The figure [1.1](#fig:zvm2nd){reference-type="ref"
reference="fig:zvm2nd"} represents the resources of your 2nd level z/VM.
At the end of this workshop these resources will be assigned composing
your z/VM environment. **Spoiler Alert** on figure
[1.2](#fig:zvm2ndcomplete){reference-type="ref"
reference="fig:zvm2ndcomplete"}.

![Second level z/VM at the end of the
workshop](/imgs/workshop-linux.png){#fig:zvm2ndcomplete}

## Access your z/VM

It is time to start you z/VM environment. We will execute the IPL
(Initial Program Load) of your system. In a first level environment it
is done via HMC (Hardware Management Console), but we are in a
restricted environment (2nd level) and our IPL is made via the first
level z/VM.

Obtain a team number from the instructor:

        Team: _________________________________________________
        
        
        z/VM IP address: ______________________________________

Connect to the first level z/VM system, known as ZVMDEMO.

Start the TN3270 client:

![Click on \"Link Parameters\...\"](imgs/3270-1.png){#fig:32701}

![Fill the primary IP and check
\"Auto-reconnect\"](imgs/3270-2.png){#fig:32702}

After you configure the IP address you should see the logon screen.

``` REXX
    z/VM ONLINE                                                                    
                                                                                
                                                                                
                                                                                
                                 / VV          VVV MM       MM                  
                                /  VV        VVV  MMM     MMM                   
                    ZZZZZZ     /   VV      VVV   MMMM   MMMM                    
                       ZZ     /    VV    VVV    MM MM MM MM                     
                      ZZ     /     VV  VVV     MM  MMM  MM                      
                     ZZ     /      VVVVV      MM   M   MM                       
                    ZZ     /       VVV       MM       MM                        
                   ZZZZZZ /        V        MM       MM                         
                                                                                
                     built on IBM Virtualization Technology                     
                                                                                
                                                                      
                                                                                
 Fill in your USERID and PASSWORD and press ENTER                               
 (Your password will not appear when you type it)                               
 USERID   ===>                                                                  
 PASSWORD ===>                                                                  
                                                                                
 COMMAND  ===>                                                                  
                                                            RUNNING   ZVMDEMO
```

Type your userid and the password (consult your instructor) and press
enter to log in on the system (the password will be shown in blanks).

``` {samepage="true"}
USERID ===> zvmws<n> where <n> is the team number assigned to you
PASSWORD ===> zcloud
```

``` REXX
z/VM ONLINE                                                                    
                                                                                
                                                                                
                                                                                
                                 / VV          VVV MM       MM                  
                                /  VV        VVV  MMM     MMM                   
                    ZZZZZZ     /   VV      VVV   MMMM   MMMM                    
                       ZZ     /    VV    VVV    MM MM MM MM                     
                      ZZ     /     VV  VVV     MM  MMM  MM                      
                     ZZ     /      VVVVV      MM   M   MM                       
                    ZZ     /       VVV       MM       MM                        
                   ZZZZZZ /        V        MM       MM                         
                                                                                
                     built on IBM Virtualization Technology                     
                                                                                
                                  LPAR  zVMTEC                                  
                                                                                
 Fill in your USERID and PASSWORD and press ENTER                               
 (Your password will not appear when you type it)                               
 USERID   ===>  zvmwsxx                                                         
 PASSWORD ===>                                                                  
                                                                                
 COMMAND  ===>                                                                  
                                                            RUNNING   ZVMDEMO
```

If your screen shows "HOLDING" in the lower right corner press the Pause
key to clear the screen. You should next see the Stand Alone Program
Loader screen.

``` REXX
STAND ALONE PROGRAM LOADER: z/VM VERSION 6 RELEASE 3.0                         
                                                                                
 DEVICE NUMBER:   0200       MINIDISK OFFSET:   39         EXTENT:  1           
                                                                                
 MODULE NAME:     CPLOAD     LOAD ORIGIN:       1000                            
                                                                                
 --------------------------------IPL PARAMETERS-------------------------------- 
fn=SYSTEM ft=CONFIG pdnum=1 pdvol=0201                                          
                                                                                
                                                                                
 -----------------------------------COMMENTS----------------------------------- 
                                                                                
                                                                                
                                                                                
                                                                                
 ------------------------------------------------------------------------------ 
                                                                                
                                                                                
                                                                                
 9= FILELIST  10= LOAD  11= TOGGLE EXTENT/OFFSET   
```

You should next see the Stand Alone Program Loader screen.

**"Tab"** to advance the cursor to the "IPL PARAMETERS" section Enter
**"cons=0009"**.

Check the example below and **press the F10 key** to load the z/VM
system.

``` REXX
STAND ALONE PROGRAM LOADER: z/VM VERSION 6 RELEASE 3.0                         
                                                                                
 DEVICE NUMBER:   0200       MINIDISK OFFSET:   39         EXTENT:  1           
                                                                                
 MODULE NAME:     CPLOAD     LOAD ORIGIN:       1000                            
                                                                                
 --------------------------------IPL PARAMETERS-------------------------------- 
fn=SYSTEM ft=CONFIG pdnum=1 pdvol=0201 cons=0009                                
                                                                                
                                                                                
 -----------------------------------COMMENTS----------------------------------- 
                                                                                
                                                                                
                                                                                
                                                                                
 ------------------------------------------------------------------------------ 
                                                                                
                                                                                
                                                                                
 9= FILELIST  10= LOAD  11= TOGGLE EXTENT/OFFSET   
```

Enter the rest of the IPL parameters as follows.

**Press the "Pause" key whenever the lower right hand corner of your
console shows "MORE" or "HOLDING".**

When prompted to provide "start" parameters:

**Enter "cold drain noautolog"**

``` REXX
22:28:58 z/VM  V6 R3.0  SERVICE LEVEL 1301 (64-BIT)                            
 22:28:59 SYSTEM NUCLEUS CREATED ON 2013-06-28 AT 14:58:28, LOADED FROM M01RES  
22:28:59                                                                        
22:28:59 ****************************************************************       
22:28:59 * LICENSED MATERIALS - PROPERTY OF IBM*                        *       
22:28:59 *                                                              *       
22:28:59 * 5741-A07 (C) COPYRIGHT IBM CORP. 1983, 2013. ALL RIGHTS      *       
22:28:59 * RESERVED. US GOVERNMENT USERS RESTRICTED RIGHTS - USE,       *       
22:28:59 * DUPLICATION OR DISCLOSURE RESTRICTED BY GSA ADP SCHEDULE     *       
22:28:59 * CONTRACT WITH IBM CORP.                                      *       
22:28:59 *                                                              *       
22:28:59 * * TRADEMARK OF INTERNATIONAL BUSINESS MACHINES.              *       
22:28:59 ****************************************************************       
22:28:59                                                                        
 22:28:59 HCPZCO6718I Using parm disk 1 on volume VMCOM1 (device 0201).         
 22:28:59 HCPZCO6718I Parm disk resides on cylinders 1 through 120.             
22:28:59 Start ((Warm|Force|COLD|CLEAN) (DRain) (DIsable)  (NODIRect)           
22:28:59       (NOAUTOlog)) or (SHUTDOWN)                                       
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
cold drain noautolog     
```

When prompted to "Change TOD clock":

**Press enter to accept the default "no"**

If necessary, press PAUSE BREAK (The status "MORE..." on the lower right
on the console).

When prompted to continue with "COLD start":

**Enter "go"**

``` REXX
22:33:40 HCPWRS2513I                                      _______              
 22:33:40 HCPWRS2513I Total files to be deleted                  1              
 22:33:42 HCPWRS2511A                                                           
 22:33:42 HCPWRS2511A Spool files will be deleted because of COLD start.        
 22:33:42 HCPWRS2511A No files have been deleted yet.                           
 22:33:42 HCPWRS2511A To continue COLD start and delete files, enter GO.        
 22:33:42 HCPWRS2511A To stop COLD start without deleting files, enter STOP.    
22:33:48 GO                                                                     
 22:33:49 HCPWRS2512I Spooling initialization is complete.                      
22:33:49 DASD 0205 dump unit CP IPL pages 19863                                 
22:33:49 HCPMLM3016I Management by the Unified Resource Manager is not available
 for this system.                                                               
22:33:49 HCPAAU2700I System gateway ZVMWSXX identified.                         
22:33:49 z/VM Version 6 Release 3.0, Service Level 1301 (64-bit),               
22:33:49 built on IBM Virtualization Technology                                 
22:33:49 There is no logmsg data                                                
22:33:49 FILES:   NO RDR,   NO PRT,   NO PUN                                    
22:33:49 LOGON AT 22:33:49 EDT SATURDAY 08/31/13                                
22:33:49 GRAF  0009 LOGON  AS  OPERATOR USERS = 1                               
22:33:49 HCPIOP952I 2048M system storage                                        
22:33:49 FILES: 0000002 RDR, 0000001 PRT,      NO PUN                           
22:33:49 HCPCRC8082I Accounting records are accumulating for userid DISKACNT.   
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                            RUNNING   ZVMWSXX   
```

You have successfully IPL'ed z/VM when you receive the following
messages:

``` REXX
22:33:40 HCPWRS2513I                                      _______              
 22:33:40 HCPWRS2513I Total files to be deleted                  1              
 22:33:42 HCPWRS2511A                                                           
 22:33:42 HCPWRS2511A Spool files will be deleted because of COLD start.        
 22:33:42 HCPWRS2511A No files have been deleted yet.                           
 22:33:42 HCPWRS2511A To continue COLD start and delete files, enter GO.        
 22:33:42 HCPWRS2511A To stop COLD start without deleting files, enter STOP.    
22:33:48 GO                                                                     
 22:33:49 HCPWRS2512I Spooling initialization is complete.                      
22:33:49 DASD 0205 dump unit CP IPL pages 19863                                 
22:33:49 HCPMLM3016I Management by the Unified Resource Manager is not available
 for this system.                                                               
22:33:49 HCPAAU2700I System gateway ZVMWSXX identified.                         
22:33:49 z/VM Version 6 Release 3.0, Service Level 1301 (64-bit),               
22:33:49 built on IBM Virtualization Technology                                 
22:33:49 There is no logmsg data                                                
22:33:49 FILES:   NO RDR,   NO PRT,   NO PUN                                    
22:33:49 LOGON AT 22:33:49 EDT SATURDAY 08/31/13                                
22:33:49 GRAF  0009 LOGON  AS  OPERATOR USERS = 1                               
22:33:49 HCPIOP952I 2048M system storage                                        
22:33:49 FILES: 0000002 RDR, 0000001 PRT,      NO PUN                           
22:33:49 HCPCRC8082I Accounting records are accumulating for userid DISKACNT.   
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                            RUNNING   ZVMWSXX   
```

**===\> Press the Pause key to clear the screen. You should see the
screen below. Observe that we still have a status of More...**

``` REXX
04: HCPGSP2627I The virtual machine is placed in CP mode due to a SIGP initial C
PU reset from CPU 00.                                                           
03: HCPGSP2627I The virtual machine is placed in CP mode due to a SIGP initial C
PU reset from CPU 00.                                                           
02: HCPGSP2627I The virtual machine is placed in CP mode due to a SIGP initial C
PU reset from CPU 00.                                                           
01: HCPGSP2627I The virtual machine is placed in CP mode due to a SIGP initial C
PU reset from CPU 00.                                                           
01: HCPGSP2627I The virtual machine is placed in CP mode due to a SIGP initial C
PU reset from CPU 00.                                                           
02: HCPGSP2627I The virtual machine is placed in CP mode due to a SIGP initial C
PU reset from CPU 00.                                                           
03: HCPGSP2627I The virtual machine is placed in CP mode due to a SIGP initial C
PU reset from CPU 00.                                                           
04: HCPGSP2627I The virtual machine is placed in CP mode due to a SIGP initial C
PU reset from CPU 00.                                                           
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                            MORE...   ZVMDEMO  
```

**===\> press the Pause key to clear the screen again**

Now, type the following command to discover who use you are.

``` REXX
===> query userid
```

Type the following command to discover how was the last IPL performed.

``` REXX
===> query cpload
```

``` REXX
12:44:25 Q USERID                                                               
12:44:25 OPERATOR AT ZVMWSXX                                                    
12:44:46                                                                        
12:44:46                                                                        
12:44:46                                                                        
12:44:49 Q CPLOAD                                                               
12:44:49 Module CPLOAD was loaded from minidisk on volume M01RES at cylinder 39.
12:44:49 Parm disk number 1 is on volume VMCOM1, cylinders 1 through 120.       
12:44:49 Last start was a system IPL.                                           
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                            RUNNING   ZVMWSXX   
```

These messages provide some basic information about your system:

1.  The system identifier is zVM . It is also displayed at the lower
    right hand corner of the console.

2.  The z/VM Version 6 Release 3.0, service level (64-bit)

3.  The OPERATOR user is currently logged on (How? Command \<QUERY
    USERID\>)

4.  The system has MB of storage (memory) (message ID HCPIOP952I or
    \<QUERY STORAGE\>)

Now disconnect from the 'OPERATOR'.

``` REXX
===> DISCONNECT
```

**Press the Pause key when the console status indicates "MORE\..."**

**Press the Enter key to proceed**

You should see the logon window for your second level z/VM system. This
is the only session available to log on users. Later on, you will enable
TCP/IP which will allow multiple connections to your system.

``` REXX
z/VM ONLINE                                                                    
                                                                                
                                                                                
                                                                                
                                 / VV          VVV MM       MM                  
                                /  VV        VVV  MMM     MMM                   
                    ZZZZZZ     /   VV      VVV   MMMM   MMMM                    
                       ZZ     /    VV    VVV    MM MM MM MM                     
                      ZZ     /     VV  VVV     MM  MMM  MM                      
                     ZZ     /      VVVVV      MM   M   MM                       
                    ZZ     /       VVV       MM       MM                        
                   ZZZZZZ /        V        MM       MM                         
                                                                                
                     built on IBM Virtualization Technology                     
                                                                                
                                                                                
                                                                                
 Fill in your USERID and PASSWORD and press ENTER                               
 (Your password will not appear when you type it)                               
 USERID   ===>                                                                  
 PASSWORD ===>                                                                  
                                                                                
 COMMAND  ===>                                                                  
                                                            RUNNING   ZVMWSXX   
```

::: bclogo
Attention It is important to ***disconnect***, **not** ***log off***
your system. Logging off from the system is equivalent to turning off
the computer, thus crashing your z/VM system.
:::

# The Control Program (CP)

## Introduction

z/VM has two main components:

-   The Control Program (CP) controls the Real Machine.

-   The Conversational Monitor System (CMS) controls Virtual Machines.

The CP is sometimes called the hypervisor. It is the layer between the
hardware and virtual machines. Each virtual machine appears to have its
own CPU, storage (memory), and devices. In reality, these items can be:

-   Real. For example, you can dedicate a real network interface to a
    virtual machine for its exclusive use.

-   Shared. For example, the CPU is shared through time sharing and real
    storage is shared as virtual storage.

-   Simulated. For example, a virtual switch is a simulated LAN
    networking switch. CP transparently maps virtual devices and
    resources to their real counterparts.

-   Emulated. For example, FBA emulated SCSI disks. The EDEVICE
    statements allow us to define emulated devices that represent real
    devices to CP. This means SCSI devices.

The following topics explain how CP manages computer resources for
virtual machines.

## Central processing units (CPUs)

::: easylist
& An IBM z13 server can have up to 141 processors. & An IBM zEnterprise
EC12 server can have up to 101 processors. & z13 can have up to 80
LPARs, an increase from 65 in zEC12 & A Logical Partition can now have
up to 141processors defined in z13 (101 for zEC12) & A z/VM LPAR can
have up to 64 logical processors if SMT is disabled or 32 logical
processors defined if SMT is enabled. & Virtual processors (or virtual
CPUs) in single virtual machine (architected): 64 && But NVirtual \>
NLogical is not usually practical & If capable of running in
multiprocessor mode, your virtual machine operating system dispatches
work on its virtual CPUs as if it were running on real hardware. CP
handles the dispatching of work on your virtual CPUs to real CPUs. &
**Guideline:** Never give a virtual machine more virtual CPUs than there
are real CPUs.
:::

Usually virtual machines share all CPUs, but a real CPU can be dedicated
to a virtual machine, which means that the CPU is reserved for that
virtual machine's exclusive use. This obviously has an impact on the
performance of other virtual machines in the system.

## Storage

Mainframe storage is analogous to memory in a personal computer. CP
commands refer to memory as storage, so do not confuse the term
"storage" with disk or tape storage.

Each virtual machine has its own virtual storage. CP manages the
residency of virtual machine's pages in real storage through paging.
Pages that have not been referenced can be moved out of real storage
into a paging device. When a virtual machine requires a page no longer
in real storage, a page fault occurs and CP brings the missing page back
into real storage.

CP has facilities that allow portions of real storage to be shared by
many virtual machines. Such portions are called shared segments. This
sharing economizes on real storage and requires less paging, thereby
improving performance. For example, the CMS nucleus is shared in real
storage by all virtual machines that loaded CMS by name; that is, every
CMS virtual machine maps a 1 MB segment of virtual storage to the same 1
MB of real storage.

## DASD and minidisks

DASD, the mainframe term for disk drives, stands for "direct access
storage device" and is analogous to a hard disk drive on a personal
computer. A single real DASD is called a volume or real volume. Each
volume has a label or volume serial number (volser) that identifies the
volume to z/VM.

It is important to understand the way that z/VM shares DASD. CP can
logically partition real DASD volumes into minidisks, which is analogous
to dividing a personal computer hard disk into multiple partitions. A
minidisk has its own label, which is distinct from the real DASD label.
Each virtual machine can have one or more minidisks and those minidisks
are under control of the guest operating system.

## Temporary minidisks

You can create a temporary minidisk from a special pool of real disks.
The disk lasts as long as the virtual machine is logged on. At logoff,
the temporary minidisk is deleted and the space returned to the
available temporary disk pool.

## Virtual Disks in storage

Virtual disks in storage are similar to temporary minidisks, except the
disks are mapped to storage rather than the cylinders of real disks.
Using virtual disks in storage avoids the need for disk I/O. CP manages
the virtual disk pages as part of its real memory management.

## Virtual readers, punches, and printers

These devices are not associated with real devices, but are implemented
through the spool file system.

In the early days of computing, input to the computer came from punched
cards loaded into a card reader. You used a key punch to record your
program on punched cards, then loaded the cards into a card reader,
which interpreted your cards and loaded your program into the computer.
Output from the program was written to a printer. z/VM preserves this
bit of computing history through virtual reader, punch, and printer
devices, also called unit record devices. Unit record devices provide a
handy way to send files from one virtual device to another, to other
virtual machines, or to real devices (such as real printers). For
instance, you can think of a file being sent from one virtual machine to
another as the virtual equivalent of taking a card stack from one
computer and loading the stack onto another computer's card reader.

Behind the manipulation of these files is a CP file system called the
spool file system. CP manages spool files on one or more DASD volumes
that act as temporary storage areas. A spool file is a collection of
data along with device control instructions for processing on a unit
record device. Spooling is the processing of files created by or
intended for virtual readers, punches, and printers. Through CP and CMS
commands, you can send spool files from one virtual device to another,
from your virtual machine to another, and to real devices.

By convention, each virtual machine has a virtual reader at virtual
device number **00C**, a virtual punch at virtual device number **00D**,
and a virtual printer at virtual device number **00E**. Your virtual
reader is like the in-box of an e-mail system, except more than just
e-mail can be placed there. Through your virtual punch, you can place a
copy of an entire operating system into the system spool, then use the
CP IPL command to load and run that operating system in your virtual
machine.

## The virtual machine console

The virtual machine console or virtual console is the primary interface
to the virtual machine. When you log on to a virtual machine from a
local terminal or a remote workstation, the virtual console is
associated with the terminal session. From the console, you can enter CP
commands, such as loading (IPL) an operating system. The virtual console
is the device an operating system views as its system or hardware
console.

As you do work on your console, the **lower right corner** of the screen
displays various status notices. The notices tell you what is happening
in the system at the present time. If you forget what these notices
mean, you can come back to this section for reference.

::: {#tab:consolestatus}
  **CP READ**        This notice means that the Control Program (CP) is waiting for you to enter a command.
  ------------------ -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  **VM READ**        This notice means that a virtual machine operating system, such as CMS, is waiting for you to enter a command.
  **RUNNING**        This notice means the virtual machine is working on something. For CMS, this means CMS is ready for you to enter a command.
  **MORE\...**       This notice means that there is more information than can fit on the current screen. After a pause (which depends on the terminal settings for your virtual machine), the next screen of information is displayed. To view the next screen right away, press the Clear key. To hold this information on the screen, press the Enter key, which changes **MORE\...** to **HOLDING**.
  **HOLDING**        This notice means the system is waiting for you to clear the screen before showing you more information. The notice appears when the screen displays **MORE\...** and you press the Enter key. The notice can also appear when another user sends you a message. To cancel the hold, press the Clear key.
  **NOT ACCEPTED**   This notice means that the system is working on something and is too busy to accept another command. Wait several seconds and issue your command again.

  : Console Status
:::

## The User Directory

The z/VM user directory (or user registry) describes the configuration
and operating characteristics of each virtual machine that can be
created by CP. A z/VM user directory exists in two forms: a source form
that consists of one or more CMS files, and an object form, compiled
from the source, on a CP-formatted disk.

Each virtual machine has a directory entry.

![User Directory example](imgs/userdir.png)

## Exercises

### Logon to MAINT

Log on to the MAINT virtual machine. The default password have changed
in the **z/VM 6.3**. In our environment the default password is
**WD5JU8QP** (or the name of the machine in some cases). Always check
the Installation Guide that comes with yours IBM Shopz order.

Alternatively, clear the logon screen, from CP mode, enter **===\> logon
maint WD5JU8QP**

``` REXX
USERID ===> maint 
PASSWORD ===> WD5JU8QP (complex password - installation default) 
```

**Press the ENTER key once to show some messages of the PROFILE EXEC for
this userid.**

**Press the ENTER key again to continue and to change the status from
"VM READ" to "RUNNING" status.**

The "PROFILE EXEC" will run to set up your environment. You are now in
CMS. It is similar to TSO in z/OS or a shell in Linux. You will learn
more about CMS in the next unit. Now, let's do some CP commands to get
familiarized with the system.

``` REXX
LOGON MAINT                                                                     
z/VM Version 6 Release 3.0, Service Level 1301 (64-bit),                        
built on IBM Virtualization Technology                                          
There is no logmsg data                                                         
FILES:   NO RDR,   NO PRT,   NO PUN                                             
LOGON AT 22:42:30 EDT SATURDAY 08/31/13                                         
z/VM V6.3.0    2013-08-31 21:23                                                 
                                                                                
DMSACP723I B (5E5) R/O                                                          
DMSACP723I D (51D) R/O                                                          
DMSACP723I E (551) R/O                                                          
                                                                                
******************************************************************              
                                                                                
THE MAINT630 USER ID **MUST** BE USED INSTEAD OF MAINT                          
WHEN INSTALLING SERVICE.                                                        
                                                                                
******************************************************************              
                                                                                
PRESS ENTER TO CONTINUE                                                         
                                                                                
Ready; T=0.01/0.01 22:43:44                                                     
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                            RUNNING   ZVMWSXX   
```
