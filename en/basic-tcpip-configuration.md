---
order: 75
icon: ":link:"
---
# Basic TCP/IP Configuration

## TCPIP Service Machines

This section describes the virtual machines that are necessary to
provide basic and optional TCP/IP services. The virtual machines listed
here comprise a set of "default" TCP/IP virtual machines that are
defined as part of the z/VM system when it is installed.

While various TCP/IP virtual machines have specific definition
requirements, all TCP/IP servers must maintain links to the following
minidisks, to allow for correct operation:

::: {#tab:tcpip}
  **Minidisk**   **Description**
  -------------- -------------------------------------------
  TCPMAINT 592   Client-code disk
  TCPMAINT 591   Server-code disk
  TCPMAINT 198   Configuration file, or customization disk

  : TCPIP Service Machines
:::

## Required Virtual Machines

The following virtual machines are required to provide basic TCP/IP
services:

::: {#tab:tcpip2}
  **Machine**   **Function**
  ------------- -----------------------------------------------------------------------------------------------------------------------------------
  6VMTCP30      Maintains the TCP/IP system. Installation and service resources are owned by this user ID.
  TCPIP         Provides TCP/IP communication services. The Telnet server is implemented as a "internal client" within the TCPIP virtual machine.
  TCPMAINT      Owns TCP/IP production resources --- the 198, 591, and 592 disks.

  : Required Virtual Machines
:::

## Optional Virtual Machines

There are many optional virtual machines that you can setup to perform
TCP/IP server functions. Some of these servers include FTPSERVE, IMAP,
PORTMAP, NAMESRV, SSLSERV, SMTP, etc.

## Configuration files

This section lists the various TCP/IP configuration files which are
necessary to provide basic TCP/IP services for most environments.

The first file, IBM DTCPARMS, contains server configuration definitions.
The next three files, PROFILE TCPIP, HOSTS LOCAL, and ETC HOSTS, are
configuration files for the TCPIP server virtual machine. The next two
files, TCPIP DATA and ETC SERVICES, need to be accessible to all TCP/IP
servers, applications, and users; these files contain information that
is (or may be) referenced by all users. ETC GATEWAYS contains routing
information for distant networks and hosts.

### The DTCPARMS files

Configuration of each server is controlled by a set of files with a file
type of DTCPARMS. These files may contain two types of information:

::: easylist
& Server class names that define the application protocols available for
all server virtual machines. & Individual server user IDs and their
associated server class, as well as the operational characteristics of
the server (security, devices, parameters, etc.).
:::

The TCP/IP server initialization program searches for server definitions
in a hierarchical fashion. The following table lists the DTCPARMS files
in the order that they are searched, along with a description of each
file.

::: {#tab:tcpip3}
  **File**          **Purpose**
  ----------------- -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  userid DTCPARMS   Can be used for servers that do not require configuration by the TCP/IP administrator, such as a test server. Such a file might commonly reside on a server disk or directory accessed at file mode A.
  nodeid DTCPARMS   Useful for shared-DASD configurations. The node ID used is the node ID returned by the CMS IDENTIFY command. This file should be maintained on the TCPMAINT 198 disk.
  SYSTEM DTCPARMS   Most customized server configurations should be maintained in this file. This file should be maintained on the TCPMAINT 198 disk.
  IBM DTCPARMS      Server classes provided by IBM, as well as the default server configurations, are supplied by this file. This file resides on the TCPMAINT 591 disk and should never be modified, because it can be replaced when service is applied, or when a new release is installed. All modifications required for your installation should be placed in SYSTEM DTCPARMS (or, nodeID DTCPARMS, as warranted).

  : The DTCPARMS files
:::

### The TCPIP DATA File

The TCPIP DATA file defines system parameters used by TCP/IP client
applications. It is used to specify configuration information for single
or multiple host systems. It also allows you to specify:

::: easylist
& Host name of the VM host & User ID of the TCPIP virtual machine &
Domain origin of the host & Output trace & Name server specifications
:::

A sample TCP/IP DATA file is shipped as TCPIP SDATA on the TCPMAINT 592
disk.

### The ETC HOSTS Files

The local host files contain information needed for local host name
resolution. Any domain name or IP address specified in this file is
accessible for use on your network. Local host files are used to create
the site table, which enables name resolution and reverse name
resolution without using a domain name server.

TCP/IP for z/VM offers two local host files for domain name resolution
and reverse name resolution. The old HOSTS LOCAL file (which supports
IPv4 only), and the preferred ETC HOSTS file (which supports both IPv4
and IPv6).

The ETC HOSTS file does not require additional processing to create the
site tables used for name resolution. The site tables are created
dynamically by the resolver when the ETC HOSTS file is used. Use of the
HOSTS LOCAL file requires that you run the MAKESITE command to create
the site tables. Whenever changes are made to the HOSTS LOCAL file, you
must run the MAKESITE command to recreate the site tables.

A sample file, ETCHOSTS SAMPLE, is supplied with the TCP/IP distribution
tapes on the.

### The TCPIP server profile file

When the TCPIP virtual machine is started, TCP/IP operation and
configuration parameters are read from an initial configuration file.
TCP/IP searches for an initial configuration file in the following order
and uses the first file present in that order:

::: easylist
& userid TCPIP, where userid is the user ID of the of the TCP/IP server
& node_name TCPIP, where node_name is the system node name returned by
the CMS IDENTIFY command & PROFILE TCPIP
:::

This file is used to customize your system, specify system operation,
Telnet, and network parameters. If no file is found, TCP/IP uses server
default values.

A sample initial configuration file is provided as PROFILE STCPIP on the
TCPMAINT 591 disk.

## Exercises

### IPWIZARD

You can initially configure TCP/IP via the IPWIZARD command which is
generally used just once. After IPWIZARD creates the initial
configuration files, they are typically maintained manually.

::: easylist
& Log on as MAINT. & The IPWIZARD command is on the MAINT 193 disk.
Issue the ACCESS command so you will pick up IPWIZARD from that
minidisk.

```
==> acc 193 g
```

& Invoke the IPWIZARD.

```
==> ipwizard
```

& At the '\*\*\* z/VM TCP/IP Configuration Wizard \*\*\*' panel. Fill in
the following data:

```
    *** z/VM TCP/IP Configuration Wizard ***                 
                                                                             
The items that follow describe your z/VM host                                
                                                                             
User ID of VM TCP/IP Stack Virtual Machine:   TCPIP___                       
                                                                             
Host Name:     (*| \textcolor{red}{ZVMWSxx} |*) (your group name) ___________                                          
Domain Name:   (*| \textcolor{red}{mycompany.com} |*)_________                      
                                                                             
Gateway IP Address:  (*|\textcolor{red}{YOUR\_GW\_ADDR}|*)_______________________                 
                                                                             
DNS Addresses:                                                               
1) (*|\textcolor{red}{YOUR\_DNS}|*)_______                                                           
2) _______________                                                           
3) _______________                          
       PF1 = HELP   PF3 = QUIT   PF8 = Continue   ENTER = Refresh    
```

& Continue with next step.

**Press the F8 key**

& At the '\*\*\* General Interface Configuration Panel \*\*\*' panel.
Fill in the following data:

```
                  *** General Interface Configuration Panel ***             
                                                                            
Interface Name:  ETH0____________     Device Number:  0800                  
                                                                            
IP Address:      (*|\textcolor{red}{YOUR\_IP\_ADDR}|*) (your team IP number)_                                            
Subnet Mask:     (*|\textcolor{red}{YOUR\_MASK}|*)__                                            
                                                                            
Path MTU Discovery (Optional):  _ Enabled      _ Disabled                   
                                                                            
Interface Type (Select one): (*|\textcolor{red}{Ask the layer to the instructor and put an 'X' there}|*)
                                                                            
   _    QDIO (layer 3)      _    QDIO (layer 2)      _    LCS               
   _    HiperSockets        _    CTC                                        
                                                                            
                                                                            
                                                                            
PF1 = HELP  PF3 = QUIT  PF7 = Backward  PF8 = Continue  ENTER = Refresh   
```

& Continue with next step.

**Press the PF8 key**

& At the '\*\*\* QDIO Interface Configuration Panel \*\*\*' panel. Fill
in the following data:

```
                   *** QDIO Interface Configuration Panel ***                
                                                                             
VLAN ID (optional):  ____                                                    
                                                                             
                                                                             
Router Type (Select one):                                                    
                                                                             
   _    Primary             _    Secondary           X    None               
                                                                             
                                                                             
Maximum Transmission Unit (MTU) size:  1500_                                 
                                                                             
                                                                             
Port Number (optional):  __                                                  
                                                                             
                                                                             
 PF1 = HELP  PF3 = QUIT  PF5 = Process  PF7 = Backward  ENTER = Refresh      
```

& Start the network configuration.

**Press the PF5 key to Process**

& The TCP/IP stack (TCPIP) must be restarted as part of this procedure.
Would you like to restart TCPIP and continue?

**Enter '1' for Yes**

& The TCP/IP configuration is complete when you see these messages:

```
Successfully PINGed Interface (172.24.200.222)                    
Successfully PINGed Gateway (172.24.200.1)                        
Successfully PINGed DNS (172.24.200.241)                          
DTCIPW2519I Configuration complete; connectivity has been verified
DTCIPW2520I File PROFILE TCPIP created on TCPIP 198               
DTCIPW2520I File TCPIP DATA created on TCPIP 592                  
DTCIPW2520I File SYSTEM DTCPARMS created on TCPIP 198             
(*|\textcolor{red}{IUGIPW8392I IPWIZARD EXEC ENDED SUCCESSFULLY                      }|*)
DMSVML2061I TCPIP 592 released                                    
Ready; T=0.15/0.23 23:50:42      
```

& At this point, your z/VM system should be on the network. Go to a DOS
prompt (or Linux session) and try to ping your z/VM system.

```
# ping <your ip>
```
:::

### Viewing TCP/IP configuration files

Let's learn what the IPWIZARD did for you.

::: easylist
& Logon to the TCPMAINT virtual machine

**Logoff of MAINT**

**Logon to TCPMAINT**

```
LOGON TCPMAINT                                          
z/VM Version 6 Release 3.0, Service Level 1301 (64-bit),
built on IBM Virtualization Technology                  
There is no logmsg data                                 
FILES: 0009 RDR,   NO PRT,   NO PUN                     
LOGON AT 10:19:32 EDT MONDAY 09/23/13                   
z/VM V6.3.0    2013-09-13 00:01                         
                                                        
Profile..: Spooling console to self (TCPMAINT)...       
Profile..: Setting PF Keys...                           
PF12 RETRIEVE BACKWARD                                  
PF24 RETRIEVE BACKWARD                                  
Profile..: Setting minidisk environment workspace...    
DMSACC724I 191 replaces A (191)                         
                                                        
Profile..: Setup complete                               
Ready; T=0.01/0.01 10:19:32        
```

& List the CMS disks that are accessed via the QUERY DISK command. Note
that the TCPMAINT 198 disk is accessed as your disk:

```
===> q disk
```

```
q disk                                                                          
LABEL  VDEV M  STAT   CYL TYPE BLKSZ   FILES  BLKS USED-(%) BLKS LEFT  BLK TOTAL
TCM191 191  A   R/W     7 3390 4096        2         11-01       1249       1260
TCM198 198  D   R/W     9 3390 4096        4         11-01       1609       1620
TCM591 591  E   R/W   122 3390 4096      166       7139-33      14821      21960
TCM592 592  F   R/W   140 3390 4096      858      10312-41      14888      25200
MNT190 190  S   R/O   207 3390 4096      696      18085-49      19175      37260
MNT19E 19E  Y/S R/O   500 3390 4096     1123      30403-34      59597      90000
MNT191 120  Z   R/O   175 3390 4096        7         24-01      31476      31500
Ready; T=0.01/0.01 10:24:34                                                     
```

& This is an important disk for TCP/IP configuration files. List all the
files on this disk. What is the command?

===\>

```
TCPMAINT FILELIST A0  V 169  Trunc=169 Size=2 Line=1 Col=1 Alt=0               
Cmd   Filename Filetype Fm Format Lrecl    Records     Blocks   Date     Time   
      PROFILE  TCPIP    D1 V         73         55          1  9/19/14 14:39:11 
      SYSTEM   DTCPARMS D1 V         71          7          1  9/19/14 14:38:58 
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
    
                                                                            
                                                                                
1= Help       2= Refresh  3= Quit   4= Sort(type)  5= Sort(date)  6= Sort(size) 
7= Backward   8= Forward  9= FL /n 10=            11= XEDIT/LIST 12= Cursor     
                                                                                
====>                                                                           
                                                            X E D I T  1 File   
```
:::

#### PROFILE TCPIP

::: easylist
& Look at the file PROFILE TCPIP. You can type an **X**, which is a
synonym for **X**EDIT right in the FILELIST command next to the file you
want to edit. & Search for the string DEVICE. You should see many of the
values that you typed into the IPWIZARD. Following is an example file
for **ZVMWSXX**:

```
===> /device
```

```
PROFILE  TCPIP    D1  V 80  Trunc=80 Size=55 Line=37 Col=1 Alt=0               
                                                                                
00037 DEVICE DEV@0800  OSD 0800   NONROUTER                                     
00038 LINK ETH0 QDIOETHERNET DEV@0800   MTU 1800 IP                             
00039 ; (End DEVICE and LINK statements)                                        
00040 ; ----------------------------------------------------------------------  
00041 ; ----------------------------------------------------------------------  
00042 HOME                                                                      
00043 172.24.200.222 255.255.255.0 ETH0                                         
00044 ; (End HOME Address information)                                          
00045 ; ----------------------------------------------------------------------  
00046 GATEWAY                                                                   
00047 ; Network       Subnet          First           Link             MTU      
00048 ; Address       Mask            Hop             Name             Size     
00049 ; ------------- --------------- --------------- ---------------- -----    
00050 DEFAULTNET                      172.24.200.1    ETH0             1800     
00051 ; (End GATEWAY Static Routing information)                                
00052 ; ----------------------------------------------------------------------  
00053 START DEV@0800                                                            
00054 ; (End START statements)                                                  
00055 ; ----------------------------------------------------------------------  
00056 * * * End of File * * *                                                   
                                                                                
                                                                                
 
                                                                                
                                                                                
                                                                                
====>                                                                           
```

& Exit this file, enter:

```
 ===> qq
```
:::

#### SYSTEM DTCPARMS

::: easylist
& Now look at the file SYSTEM DTCPARMS. & You should see the following:

```
SYSTEM   DTCPARMS D1  V 80  Trunc=80 Size=7 Line=0 Col=1 Alt=0                 
                                                                                
00000 * * * Top of File * * *                                                   
00001 .**********************************************************************   
00002 .* SYSTEM DTCPARMS created by DTCIPWIZ EXEC on 19 Sep 2013                
00003 .* Configuration program run by MAINT at 23:50:15                         
00004 .**********************************************************************   
00005 :nick.TCPIP    :type.server                                               
00006                :class.stack                                               
00007                :attach.0800-0802                                          
00008 * * * End of File * * *                                                   
                                                                                
                                                                                
 
                                                                                
====>                                                                           
```

This file is how the OSA devices 800, 801 and 802 are attached to the
TCPIP service machine.

& Exit this file:

**Press the F3 key (or type qq)**

& If you are still in FILELIST mode, exit and return to RUNNING mode.

**Press the F3 key**
:::

### Renaming the PROFILE TCPIP file

One change that is recommended is to rename your main configuration
file, PROFILE TCPIP. It is possible that applying service to z/VM can
overwrite the PROFILE TCPIP file.

::: easylist
& Use the RENAME command to change the file:

```
===> rename profile tcpip d zvmwsxx = d
```

& Now you should test this change. You can do this by forcing the TCPIP
user ID off the system (logging it off) and then logging on
interactively and watching it come back up. This is analogous to a Linux
"service network restart" command. Be careful when you do this. If you
are using the network to get to your system, you will immediately lose
the connection.

```
===> force tcpip
```

& Now logon to TCPIP and start the TCP/IP stack.

```
===> logoff TCPMAINT
===> logon TCPIP
```

Press **Enter** to run the PROFILE EXEC

Press **Enter** again to start the TCP/IP stack. Note that your renamed
profile is used:

```
LOGON TCPIP                                                                     
z/VM Version 6 Release 3.0, Service Level 1301 (64-bit),                        
built on IBM Virtualization Technology                                          
There is no logmsg data                                                         
FILES:   NO RDR, 0001 PRT,   NO PUN                                             
LOGON AT 12:25:00 EDT MONDAY 09/23/13                                           
z/VM V6.3.0    2013-09-13 00:01                                                 
                                                                                
DMSACP723I D (198) R/O                                                          
DMSACP723I E (591) R/O                                                          
DMSACP723I F (592) R/O                                                          
Ready; T=0.01/0.01 12:25:03                                                     
DTCRUN1022I Console log will be sent to default owner ID: TCPMAINT              
DTCRUN1046I Using 'server' definition (TCPIP) from file: SYSTEM DTCPARMS D1     
DTCRUN1046I Using 'class' definition (STACK) from file: IBM DTCPARMS E1         
DTCRUN1096I STORAGE = 128M                                                      
DTCRUN1038I Server is not configured to support secure connections              
DTCRUN1021R To cancel TCP/IP Stack startup, type any non-blank character and    
            press ENTER. To continue startup, just press ENTER.                 
                                                                                
DTCRUN1011I Server started at 12:25:07 on 23 Sep 2013 (Monday)                  
DTCRUN1011I Running server command: TCPIP                                       
DTCRUN1011I No parameters in use                                                
DTCRUN1048I SLVL service information for: TCPIP MODULE E2                       
DTCRUN1048I CMHOSTN  ZVM620    CMPRCOM  ZVM630    CMTCPPR  ZVM630               
DTCRUN1048I CMUNTOK  ZVM620    FPIPDOW  ZVM630    FPI6DOW  ZVM630               
DTCRUN1048I FPNOTIF  ZVM630    FPPARSE  ZVM630    FPQDIO   ZVM630               
DTCRUN1048I FPQUEUE  PK30609   FPROUND  ZVM620    FPSCHED  ZVM630               
DTCRUN1048I FPTCPDOW ZVM630    FPTCPREQ ZVM620    FPTCPUP  ZVM620               
DTCRUN1048I FPTIMER  ZVM620    FPTOCLAW PQ95126   FPTOCTC  PQ70168              
                                                                                
                                                            MORE...   ZVMWSXX   
```

```
DTCRUN1048I TCMIB    ZVM630    TCMON    ZVM630    TCMPRIO  ZVM620               
DTCRUN1048I TCNED    ZVM630    TCNOTIF  ZVM630    TCOFFLOP PQ21340              
DTCRUN1048I TCPARSE  ZVM630    TCPDOWN  ZVM620    TCPERUP  PQ49456              
DTCRUN1048I TCPEXITS ZVM620    TCPIP    ZVM620    TCPIPIN  ZVM630               
DTCRUN1048I TCPKT    ZVM620    TCPREQU  ZVM630    TCPRINT  ZVM630               
DTCRUN1048I TCPSSL   PM77039   TCPUP    ZVM630    TCQDIO   ZVM630               
DTCRUN1048I TCQUEUE  ZVM620    TCROUND  ZVM620    TCSOCKRE PM72267              
DTCRUN1048I TCSYSIO  PQ51738   TCTOATM  ZVM620    TCTOCLAW PQ95126              
DTCRUN1048I TCTOCTC  ZVM620    TCTOHPPI PQ51891   TCTOIUC  ZVM630               
DTCRUN1048I TCTOOSD  ZVM630    TCTOPC3  PQ66267   TCTREEP  ZVM620               
DTCRUN1048I TCUDPRE  ZVM620    TCUDPUP  ZVM630    TCUTIL   PM71046              
DTCRUN1048I TCVMSUB  ZVM620    TNLDSFP  ZVM630    TNSTMAS  ZVM630               
DTCRUN1048I TNTOCP   ZVM620    TNTOTCP  ZVM620    T6PREQU  ZVM630               
DTCRUN1048I T6PSSL   ZVM630    T6SOCKRE ZVM630                                  
DTCTCP001I z/VM TCP/IP Level 630                                                
                              ***** 09/23/13 *****                              
12:23:09 DTCIPI008I    Initializing... TCPIP MODULE E2 dated 05/22/13 at 14:18  
12:23:09 DTCIPI052I TCP/IP Module Load Address:  00C00000                       
12:23:09 DTCIPI009I Devices will use diagnose 98 real channel program support   
12:23:09 DTCIPI012I TCP/IP running under z/VM system                            
12:23:09 DTCIPI005I Trying to open TCPIP TCPIP *                                
12:23:09 DTCIPI005I Trying to open ZVMWSXX TCPIP *                              
12:23:09 DTCIPI006I Using profile file ZVMWSXX TCPIP D1 dated 09/19/13 at 23:50 
12:23:09 DTCFPM004I FPSM is available with 0 pages allocated                    
12:23:09 DTCFPM005I FPSM may allocate an additional 24570 pages out of 27301    
                              ***** 09/23/13 *****                              
12:23:09 DTCPRS354I AutoRestart is the default for device DEV@0800              
12:23:09 DTCOSD080I OSD initializing:                                           
12:23:09 DTCPRI385I  Device DEV@0800:                                           
12:23:09 DTCPRI386I     Type: OSD, Status: Not started                          
                                                                                
                                                            MORE...   ZVMWSXX   
```

```
10:32:22 DTCQDI001I QDIO device DEV@0800 device number 0800:                    
10:32:22 DTCQDI007I   Enabled for QDIO data transfers                           
10:32:22 DTCOSD238I ToOsd: IPv4 multicast support enabled for DEV@0800          
10:32:22 DTCOSD319I ProcessSetArpCache: Supported for device DEV@0800           
10:32:22 DTCOSD341I Obtained MAC address 02003000001E for device DEV@0800       
10:32:22 DTCOSD246I OSD device DEV@0800: Assigned IPv4 address 172.24.200.95    
10:32:22 DTCOSD234I ToOsd: TCPIP host is not set as a router for port UNASSIGNED
 for IPv4                                                                       
                                                   
                                                                                
                                                            RUNNING   ZVMWS01   
```

& Now should you LOGOFF of TCPIP or DISCONNECT?

===\>

The former will kill the stack while the latter will allow it to run. A
VM service machine is analogous to a Linux daemon. Use the #CP DISC
command. The #CP *punches through* to the CP level.
:::

### Verify the TCPIP setup

If all went well, your z/VM should now be on the network. Let's try a
few things to verify the configuration is correct.

::: easylist
& Start a TN3270 session and connect directly to you z/VM system. You
now have the ability to log on to more than one virtual machine at a
time. & Log on as TCPMAINT. & Use the NETSTAT command to display
information about your network. Display the device information. You
should see information about the QDIO1 device.

```
===> netstat dev
```

Display the gateway information. You should see information about the
gateway.

```
===> netstat gate
```

& Ping the gateway (first hop).

```
===> ping GATEWAYIP
```

& Tracing routes through a network is sometimes necessary to help debug
connectivity issues. Try the TRACERTE command and trace the route to
another IP:

```
===> tracerte ANOTHERIP
```

& Log off the TCPMAINT virtual machine.

```
===> logoff 
```
:::

### Create a ETC HOSTS file

The local host files contain information needed for local host name
resolution. Any domain name or IP address specified in this file is
accessible for use on your network. Local host files are used to create
the site table, which enables name resolution and reverse name
resolution without using a domain name server.

A sample file, ETCHOSTS SAMPLE, is supplied with the z/VM system
deliverable on the TCPMAINT 592 disk. You can use this file as a guide
for creating a customized ETC HOSTS file, that should reside on this
same minidisk (TCPMAINT 592). Because each site is unique, the
statements within the ETC HOSTS file must be customized for your
installation.

::: easylist
& Log on as TCPMAINT.

& Identify disk 592 file mode

```
===> q disk
```

& Copy the sample file from disk 592 to ETC HOSTS

```
===> copy ETCHOSTS SAMPLE f ETC HOSTS =
```

& Edit the file and add the IP from another student

```
00046 # -----------------------------------------------------------------------
00047 # Local Host Loopback Address (IPv4 format)
00048 # -----------------------------------------------------------------------
00049
00050 127.0.0.1     localhost loopback
00051 (*|\textcolor{red}{172.24.200.222  zvmwsxx}|*)
00052
00053 # -----------------------------------------------------------------------
00054 # Special IPv6 Addresses
00055 # -----------------------------------------------------------------------
```

& Test it pinging the z/VM using its name:

```
===> ping zvmwsxxx
```

```
ping zvmwsxx
Ping Level 630: Pinging host ZVMWSXX (172.24.200.222).
                Enter #CP EXT to interrupt.
PING: Ping #1 response took 0.003 seconds. Successes so far 1.
Ready; T=0.08/0.17 19:41:26
```
:::

### Configure AUTOLOG1's PROFILE EXEC {#sec:autologtcpip}

When z/VM IPLs, normally the AUTOLOG1 virtual machine is logged on
(unless the NOAUTOLOG parameter is specified at IPL). Its PROFILE EXEC
is run when CMS IPLs. Using this file, perform the following tasks:

::: easylist
& Limit minidisk cache in main storage and turn it off in expanded
storage with the SET MDC command. & Start virtual machines that should
be started using the XAUTOLOG command
:::

::: easylist
& Log on to AUTOLOG1.

& Before pressing Enter at the VM READ prompt, type **acc (noprof** so
that the PROFILE EXEC is not run:

```
LOGON AUTOLOG1
z/VM Version 6 Release 3.0, Service Level 1201 (64-bit),
built on IBM Virtualization Technology
There is no logmsg data
FILES: NO RDR, NO PRT, NO PUN
LOGON AT 13:32:04 EDT TUESDAY 09/03/13
z/VM V6.3.0
2013-06-24 16:58
==> acc (noprof
```

& Make a copy of the original PROFILE EXEC

```
==> copy profile exec a = execorig =
```

Edit the PROFILE EXEC and add the following three lines below the
Customer processing comment:

```
==> x profile exec
====> /customer
```

```
...
/* Customer processing can be added here
*/
/*********************************************************************/
"CP XAUTOLOG TCPIP"   /* Autolog TCPIP */
"CP SET MDC STOR 0M 256M" /* Limit minidisk cache in CSTOR */
...
====> file
```

& The TCPIP VM should start automatically during the IPL.
:::

This is our actual environment after the changes:

![Environment with TCPIP](/imgs/workshop-tcpip.png){#fig:workshoptcpip}
