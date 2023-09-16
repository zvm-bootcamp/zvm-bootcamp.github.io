---
order: 90
icon: terminal
---
# CMS

## Introduction

Just as you can interact with Linux or UNIX® through a bash or Korn
shell (popular Unix shell), you can interact with z/VM through CMS. Like
a shell, you can use CMS to edit files, run EXECs (script-like
executable files) or programs, modify the virtual machine environment,
or modify z/VM itself. CMS is to z/VM as a shell is to Linux or UNIX.

## HELP

z/VM provides online help through the CMS Help system. The HELP command
is like the man command in Linux. You can find full descriptions of z/VM
commands by using the HELP command.

## Minidisks and CMS access mode

CMS, like other operating systems running in a virtual machine, can
access minidisks to store and retrieve files. For CMS, each minidisk has
an access mode represented by an alphabetic letter that determines how
CMS searches for files. In Linux, path variables defining directories
determine the search order for files. CMS searches for files among
minidisks based on the alphabetical order of the access mode. First, CMS
looks on the A minidisk, then the B minidisk, and so forth. The 191
minidisk holds a special place in CMS. A 191 minidisk to a CMS user is
like the home file directory for a Linux user. CMS always tries to
access a user's 191 minidisk as access mode A. The CMS 191 minidisk is
often called the "A-disk".

## CMS Files

CMS files have a file name, file type, and file mode. File names and
file types can be up to 8 characters long. The file mode corresponds to
the access mode of the minidisk. Examples:

```
PROFILE  EXEC    A1
MYDOC    LISTING A1
DNFPFS   LISTPS  B1
```

By convention, some file types have special meanings. For example, EXEC
is the file type for a file that contains executable statements, LISTING
is the file type for text files, and LISTPS is the file type for
PostScript files. To view and manipulate files, use the FILELIST command
(Or LISTFILE command). FILELIST is similar to the dir command in Linux.

## PROFILE EXEC

The PROFILE EXEC is a special executable file analogous to the .profile
(or .bash_profile) in Linux and UNIX. Every time a CMS user logs on, CMS
runs the PROFILE EXEC residing on the 191 minidisk, file mode A. You can
use the PROFILE EXEC to set up your virtual machine environment; for
instance, access disks, set up special PF keys, or even load another
operating system in your virtual machine.

There can be times when you do not want the PROFILE EXEC to execute when
you log on. For example, assume your PROFILE EXEC automatically loads
Linux. If you have just shut down Linux and want to start CMS, but
prevent Linux from being loaded again, you can prevent CMS from
executing the PROFILE EXEC by issuing **access (noprof**. When you IPL
(load) CMS, you see an identifier line displayed and CMS pauses with VM
READ in the lower right corner of the display.

Example (logging with NOPROF parameter)

```
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
 USERID   ===>  LINUX1                                                          
 PASSWORD ===>                                                                  
                                                                                
 COMMAND  ===>  ACCESS (NOPROF                                                  
                                                            RUNNING   ZVMWSXX   
```

## The CMS file editor XEDIT

CMS provides a file editor called XEDIT, which is a not only a
full-screen editor, but a powerful programming tool. XEDIT has functions
similar to vi in Linux.

## Exercises

### HELP

Logon as MAINT. Press the enter key until you see the RUNNING state on
the lower right hand corner of the screen.

To see a list of the CMS commands:

```
===> help 
```

Place cursor on "CMS" and press the ENTER key

What you see here is a subset of the CMS commands available in z/VM.

Display helps text for the QUERY command. You may have to scroll down
(F8) to find it.

Place cursor on "Query" and press the ENTER key

The bottom of the screen lists the PF Keys for additional options that
are available for the selected command. To see related information for
this command:

Press the F11 key

When you are done browsing, exit the help utility.

Press the F3 key repeatedly until you return to "RUNNING" state

Another way to go directly to the menu of CMS commands is with the
following help command:

```
===> help cms menu 
```

Exit the help facility and return to the READY prompt: Press the F3 key
repeatedly until you return to "RUNNING" state

### QUERY

The CMS QUERY command is used to display information about your virtual
machine. You can get information about:

::: easylist
& The operation of your virtual machine. & The status of your files and
file pool directories. & Information about how your virtual machine is
set up.
:::

Use the QUERY ACCESSED command to display the status of your accessed
disks. Note that the 191 disk is accessed as "A".

```
===> q accessed
```

```
q userid                                    
MAINT    AT ZVMWSXX                         
Ready; T=0.01/0.01 18:42:50                 

q accessed                                  
Mode  Stat     Files  Vdev  Label/Directory 
A      R/W         3  191   MNT191          
B      R/O       134  5E5   MNT5E5          
C      R/W         4  2CC   MNT2CC          
D      R/O       293  51D   MNT51D          
E      R/O         8  551   PMT551          
S      R/O       696  190   MNT190          
Y/S    R/O      1123  19E   MNT19E          
Ready; T=0.01/0.01 18:42:54                 
```

Use the QUERY IMPCP command to find out the implied CP function setting
for your virtual machine.

```
===> q impcp
```

*For the MAINT virtual machine, the IMPCP function is set to*

You will learn more about this function in a moment.

```
set impcp off              
Ready; T=0.01/0.01 14:52:34
q impcp                    
IMPCP    = OFF             
Ready; T=0.01/0.01 14:52:37

set impcp on               
Ready; T=0.01/0.01 14:52:42
q impcp                    
IMPCP    = ON              
Ready; T=0.01/0.01 14:52:44
```

### ACCESS

The ACCESS command is used to:

::: easylist
& Identify a minidisk to CMS. & Make a list of the files on the
specified minidisk or directory available to your virtual machine. &
Establish a file mode letter for the files on a minidisk or in a
directory.
:::

Use the ACCESS command to access the 191 minidisk as "Z".

```
===> access 191 z
```

What happened to the A-disk?

What implications does this change have?

Re-access the 191 minidisk as "A". What is the command? ===\>

```
q disk                                                                          
LABEL  VDEV M  STAT   CYL TYPE BLKSZ   FILES  BLKS USED-(%) BLKS LEFT  BLK TOTAL
MNT191 191  A   R/W   175 3390 4096       27        172-01      31328      31500
MNT5E6 5E6  B   R/W     9 3390 4096      134       1304-80        316       1620
MNT2CC 2CC  C   R/W    10 3390 4096        3        104-06       1696       1800
MNT51D 51D  D   R/W    26 3390 4096      293       1796-38       2884       4680
PMT551 551  E   R/W    40 3390 4096       10        119-02       7081       7200
MNT190 190  S   R/O   207 3390 4096      696      18085-49      19175      37260
MNT19E 19E  Y/S R/O   500 3390 4096     1123      30403-34      59597      90000
Ready; T=0.01/0.01 14:53:37                                                     

access 191 z                                                                    
DMSACP726I 191 A released                                                       
Ready; T=0.01/0.01 14:53:46                                                     
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                            RUNNING   ZVMWSXX   
```

### COPYFILE

The COPYFILE command is used to copy and modify files on CMS minidisks.
You can:

::: easylist
& Combine two or more files into a single file. & Copy multiple input
files into multiple output files. & Change file characteristics (such as
file mode number and record format) and/or modify file contents.
:::

Logon as MAINT Copy the file PROFILE EXEC A to a new file called PROFILE
EXECSAVE A.

```
===> copy profile exec a = execsave =
```

What do the "=" signs mean?

### CP

There are several ways to issue CP commands in CMS depending on the
setting of the implied CP (IMPCP) option:

::: easylist
& If IMPCP is set to ON, CMS will try to interpret an "unknown" command
as a CP command. & If IMPCP is set to OFF, you must precede any CP
commands with "CP"
:::

Or you can precede any CP command with "#CP" to directly send the
command to the control program.

Find your current IMPCP option setting:

```
===> q impcp
```

It should say 'ON'. This is the default. It means if CMS does not
recognize a command, it will send to CP for processing. Try a CP command
(while you are in CMS):

```
===> q dasd
```

Sure enough, the command works and you see a list of DASDs currently
attached to your virtual machine. Next set the IMPCP option to off:

```
===> set impcp off
```

Try the same CP command:

```
===> q dasd
```

This time, the command failed. With the IMPCP function turned off, CMS
did not redirect the unknown command to CP for processing.

You are currently running in the CMS environment. To enter CP mode,
type:

```
===> CP
```

The console status in the lower right hand corner of the screen now
indicates "CP READ".

Try the same CP command:

```
===> q dasd
```

To return to CMS, type:

```
===> begin (or 'b' for short) 
```

The status in the lower right hand corner of the console returns to
"RUNNING".

8\. Another way to issue CP command is to prefix it with '#CP'. Try
this:

```
===> #cp q dasd 
```

9\. Reset the IMPCP option back to ON:

```
===> set impcp on
```

### FILELIST

The FILELIST command is used to display a list of information about CMS
files residing on accessed disks. In the FILELIST environment,
information is displayed under the control of XEDIT. You can use XEDIT
subcommands to manipulate the list itself. You can also issue CMS
commands against the files directly from the displayed list.

1\. Logoff as MAINT

2\. Logon as MAINT630

3\. Display a list of files on your A-disk.

===\> filel \* \* a

The files are sorted by date and time, newest to oldest.

```
MAINT630 FILELIST A0  V 169  Trunc=169 Size=27 Line=1 Col=1 Alt=9              
Cmd   Filename Filetype Fm Format Lrecl    Records     Blocks   Date     Time   
*     $VMFP2P  $MSGLOG  A1 V         80        381          6  8/31/13 21:25:26 
      MAINT630 NETLOG   A0 V        104         11          1  8/31/13 21:25:26 
      PUT2PROD $CONS    A1 V        132        361         12  8/31/13 21:25:26 
      SETUP    $LINKS   A1 V         26         67          1  8/31/13 21:25:26 
      $VMFBLD  $MSGLOG  A1 V         80        432          6  8/31/13 21:25:21 
      $VMFMRD  $MSGLOG  A1 V         80        303          5  8/31/13 21:24:57 
      $VMFSRV  $MSGLOG  A1 V         83        821         11  8/31/13 21:24:24 
      SERVICE  $CONS    A1 V        132        790         26  8/31/13 21:24:24 
      $VMFAPP  $MSGLOG  A1 V         80        208          3  8/31/13 21:23:51 
      $VMFINS  $MSGLOG  A1 V         80        198          3  8/31/13 21:23:51 
      6VMPTK30 PSUPLAN  A1 V         72         33          1  8/31/13 21:23:49 
      6VMCPR30 PSUPLAN  A1 V         72         33          1  8/31/13 21:23:26 
      ZCMSLOAD $NUCEXEC A1 F         80       1675         33  8/31/13 21:23:22 
      CMSLOAD  $NUCEXEC A1 F         80       1675         33  8/31/13 21:23:18 
      6VMCMS30 PSUPLAN  A1 V         72         35          1  8/31/13 21:23:07 
      6VMLEN20 PSUPLAN  A1 V         72         33          1  8/31/13 21:22:55 
      6VMSES30 PSUPLAN  A1 V         72         33          1  8/31/13 21:22:47 
      VMFINS   PRODLIST A1 V        101         10          1  8/31/13 21:22:45 
      INS      6301     A1 V         39         14          1  6/28/13 15:01:51 
      6VMCPR30 $INS6301 A1 V          8         10          1  6/28/13 15:01:51 
      6VMPTK30 $INS6301 A1 V          8          7          1  6/28/13 15:01:51 
      6VMCMS30 $INS6301 A1 V          7          8          1  6/28/13 15:01:50 
      6VMSES30 $INS6301 A1 V          7          9          1  6/28/13 15:01:49 
      6VMLEN20 $INS6301 A1 V          7          7          1  6/28/13 15:01:48 
      PROFILE  EXEC     A1 V         71         37          1  4/25/13  8:00:57 
1= Help       2= Refresh  3= Quit   4= Sort(type)  5= Sort(date)  6= Sort(size) 
7= Backward   8= Forward  9= FL /n 10=            11= XEDIT/LIST 12= Cursor     
                                                                                
====>                                                                           
                                                            X E D I T  1 File   
```

Copy the PROFILE EXEC file to PROFILE EXECBKP. Press the TAB key to
advance the cursor to the CMD column of the PROFILE EXEC file

Type "COPY / = EXECBKP =" and press the ENTER key

The command can be read as "copy the current file (/) to a file with the
same file name of PROFILE (=), a file type of EXECBKP and the same file
mode of A (=)".

Update the FILELIST display and you should see the new copy of the
profile.

```
===> Press the F2 key 
```

Exit FILELIST.

```
===> Press the F3 key
```

### XEDIT

#### Configure the XEDIT PROFILE

The XEDIT command looks for the file XEDIT PROFILE configuration file
when it is invoked. Not all CMS virtual machines have a copy of this
file, so XEDIT sessions look and behave differently. The MAINT 191 (A)
disk has a PROFILE XEDIT so when you are editing files on MAINT, the
values in this profile are usually in effect.

To configure the XEDIT profile on the SSI cluster, perform the following
steps:

::: easylist
& Log on to **MAINT** if you are not already. & One default setting that
can be dangerous, especially if you use F12 to retrieve commands, is
that PF12 is set to the **FILE** subcommand. Sometimes you might not
want to save your changes with the stroke of one key. It is recommended
that you set PF12 to the ? subcommand, which has the effect of a
retrieve key:

```
==> copy profile xedit a profile xediorig a (oldd
==> x profile xedit
```

**Before:**

SET PF12 FILE

**After:**

SET PF12 ?

& Save your changes with the FILE subcommand. & Make the modified file
available to other virtual machines by copying it to the MAINT 19E disk
with file mode suffix 2: && Release the current 19E disk:

```
==> rel 19e
```

&& Link to the MAINT 19E disk read/write:

```
==> link * 19e 19e mr
DASD 019E LINKED R/W; R/O BY 10 USERS
```

&& Access the disk as file mode F:

```
==> acc 19e f
```

&& Copy it to the MAINT 19E disk (F) with file mode suffix 2 (because
the MAINT 19E disk is commonly accessed with a file mode suffix of 2,
files will not be seen by other virtual machines unless they have this
file mode suffix):

```
==> copy profile xedit a = = f2
```

&& Save the CMS named saved segment with the following commands:

```
==> acc 193 g
==> sampnss cms
HCPNSD440I The Named Saved System (NSS) CMS was successfully defined in fileid 0002.
==> ipl 190 parm savesys cms
HCPNSS440I Named Saved System (NSS) CMS was successfully saved in fileid 0002.
```
:::

The same XEDIT PROFILE should now be accessible to all virtual machines
in the z/VM system.

#### Creating an EXEC

Let's create a simple REXX EXEC file with an edit session by using the
XEDIT command.

1\. Logoff MAINT630 2. Logon as MAINT 3. Start an XEDIT session and
create a new file HELLO EXEC A.

```
===> xedit hello exec
```

4\. Enter Input mode and press Enter:

```
===> i
```

5\. Input the following text. Refer to the XEDIT quick reference guide
if you need help.

```
/* A sample REXX exec */
say 'Welcome to the z/VM and Linux on z Workshop'
say 'Hello World'
```

6\. Return to Edit mode:

Press the Enter key twice

7\. File your changes and exit the file.

```
===> file
```

8\. An REXX EXEC is similar to a Unix/Linux script. Let's run the exec
and see what happens:

```
===> hello
```

9\. Now update the HELLO EXEC file:

```
===> x hello exec 
```

10\. Insert a new line:

Type an 'i' on the line number 3

Enter the text 'q time'

```
00000 * * * Top of File * * *                             
00001 /* A sample REXX exec */                            
00002 say 'Welcome to the z/VM and Linux on z - Workshop'
00003 'query time'                                        
00004 * * * End of File * * *    
```

11\. Execute the file again. You have successfully created an executable
file to execute commands and output messages!

```
===> hello
```

Next, modify the PROFILE EXEC file so that you will gain access to a
shared utility disk which contains useful scripts for the workshop.

1\. Edit the "PROFILE EXEC" file.

```
===> x profile exec 
```

2\. Add the following statements (in upper case) after the following
line 'ACCESS 551 E':

```
'CP ATTACH 0192 *'
'ACCESS 0192 Z'
```

10\. File your changes and exit the file.

```
===> file
===> profile
```

11\. Execute the profile to pick up the changes. You should see a
message tell you that the device 192 is attached.

```
Ready; T=0.01/0.01 14:25:42                                                     
profile                                                                         
DMSACC724I 5E5 replaces B (5E5)                                                 
DMSACP723I B (5E5) R/O                                                          
DMSACC724I 2CC replaces C (2CC)                                                 
DMSACC724I 51D replaces D (51D)                                                 
DMSACP723I D (51D) R/O                                                          
DMSACC724I 551 replaces E (551)                                                 
DMSACP723I E (551) R/O                                                          
DASD 0192 ATTACHED TO MAINT 0192 WITH DEVCTL                                    
                                                                                
******************************************************************              
                                                                                
THE MAINT630 USER ID **MUST** BE USED INSTEAD OF MAINT                          
WHEN INSTALLING SERVICE.                                                        
                                                                                
******************************************************************              
                                                                                
PRESS ENTER TO CONTINUE                                                         
                                                                                
                                                          VM READ   ZVMWSXX   
```
