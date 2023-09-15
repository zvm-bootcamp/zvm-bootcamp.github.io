---
order: 50
icon: ":test_tube:"
---
# Clone Linux {#cap:clonelinux}

During this chapter we will create our second Linux (LINUX2) using
LINUX1 (from the previus chapter) as our golden image.

## Before you begin

You need to:

::: easylist
& **Be sure that LINUX1 is not running** so the new clone can use
LINUX1's IP address. You will use LINUX1's IP address temporarily for
each Linux clone during the cloning process. Since no two Linux virtual
servers can have the same IP address at the same time, you must clone
Linux servers one at a time, then customize them. & Have the new IP
address for the cloned Linux virtual server. & Log on as MAINT.
:::

## Procedure

Assuming that LINUX2 is created in exercise
[9.7.1](#sec:createlinux2){reference-type="ref"
reference="sec:createlinux2"}, we will clone all the disks from LINUX1
to LINUX2. We will use DIRMAINT for this activity.

::: easylist
& Unlock LINUX1's directory entry. From the command line, type this
command and press the Enter key:

```
dirm for linux1 unlock
DVHXMT1191I Your UNLOCK request has been sent for processing to DIRMAINT
DVHXMT1191I at SSI1.
Ready; T=0.12/0.14 16:22:44
 DVHREQ2288I Your UNLOCK request for LINUX1 at * has been accepted.
 DVHLOC3601E Directory entry LINUX1 is already unlocked.
 DVHREQ2289E Your UNLOCK request for LINUX1 at * has failed; with RC =
 DVHREQ2289E 3601.
```

& If you received the error above, it is ok. We just need to ensure that
the directory is unlocked.

& Clone the disks from LINUX1 to the clone LINUX02. From the command
line, type these DirMaint commands and press the Enter key after each
one:

```
===> dirm for linux2 clonedisk 100 linux1 100
===> dirm for linux2 clonedisk 101 linux1 101
===> dirm for linux2 clonedisk 102 linux1 102
===> dirm for linux2 clonedisk 103 linux1 103
===> dirm for linux2 clonedisk 104 linux1 104
```

::: bclogo
Hints Notice that the target is "linux2" and it is defined before
"linux1" in the command above.

Example:

*dirm for linux2 clonedisk 100 linux1 100*

The command above means: "For [linux2]{style="color: blue"}, in my mdisk
[100]{style="color: blue"} clone [linux1]{style="color: red"} mdisk
[100]{style="color: red"}"
:::

& You will receive a response like this one for each command:

```
dirm for linux2 clonedisk 100 linux1 100
DVHXMT1191I Your CLONEDISK request has been sent for processing to
DVHXMT1191I DIRMAINT at SSI1.
Ready; T=0.15/0.17 10:44:43
 DVHREQ2288I Your CLONEDISK request for LINUX2 at * has been accepted.
 DVHSCU3541I Work unit 30104444 has been built and queued for processing.
 DVHSHN3541I Processing work unit 30104444 as MAINT from SSI1,
 DVHSHN3541I notifying MAINT at SSI1, request 565 for LINUX2 sysaffin
 DVHSHN3541I *; to: CLONEDISK 0100 LINUX1 0100
 DVHBIU3450I The source for directory entry DATAMOVE has been updated.
 DVHBIU3424I The next ONLINE will take place immediately.
 DVHDRC3451I The next ONLINE will take place via delta object directory.
 DVHBIU3428I Changes made to directory entry DATAMOVE have been placed
 DVHBIU3428I online.
 DVHBIU3450I The source for directory entry DATAMOVE has been updated.
 DVHBIU3424I The next ONLINE will take place immediately.
 DVHDRC3451I The next ONLINE will take place via delta object directory.
 DVHBIU3428I Changes made to directory entry DATAMOVE have been placed
 DVHBIU3428I online.
 DVHBIU3450I The source for directory entry DATAMOVE has been updated.
 DVHBIU3424I The next ONLINE will take place immediately.
 DVHDRC3451I The next ONLINE will take place via delta object directory.
 DVHBIU3428I Changes made to directory entry DATAMOVE have been placed
 DVHBIU3428I online.
 DVHBIU3450I The source for directory entry LINUX2 has been updated.
 DVHBIU3424I The next ONLINE will take place immediately.
 DVHDRC3451I The next ONLINE will take place via delta object directory.
 DVHBIU3428I Changes made to directory entry LINUX2 have been placed
 DVHBIU3428I online.
 DVHBIU3450I The source for directory entry DATAMOVE has been updated.
 DVHBIU3424I The next ONLINE will take place immediately.
 DVHDRC3451I The next ONLINE will take place via delta object directory.
 DVHBIU3428I Changes made to directory entry DATAMOVE have been placed
 DVHBIU3428I online.
 DVHRLA3891I Your DMVCTL request has been relayed for processing.
 DVHREQ2289I Your CLONEDISK request for LINUX2 at * has completed; with
 DVHREQ2289I RC = 0.
```

& Cloning might take time. You are ready to move to the next step after
your work units have completed successfully. To determine the status of
your work units, type this command and press the Enter key:

```
===> dirm query datamove *
```

& If something is running you may see a workunit:

```
dirm query datamove *
DVHXMT1191I Your QUERY request has been sent for processing to
DVHXMT1191I DIRMAINT at SSI1.
Ready; T=0.12/0.13 10:56:46
 DVHREQ2288I Your QUERY request for MAINT at * has been accepted.
 DVHQRY3389I DATAMOVE DATAMOVE at SSI1 Status: ACTIVE WUCF:30105634
 DVHQRY3389I Pending: 1 Autologs: 0
 DVHREQ2289I Your QUERY request for MAINT at * has completed; with RC =
 DVHREQ2289I 0.
```

& Otherwise the DATAMOVE queue should be empty:

```
dirm query datamove *
DVHXMT1191I Your QUERY request has been sent for processing to
DVHXMT1191I DIRMAINT at SSI1.
Ready; T=0.11/0.13 11:12:31
 DVHREQ2288I Your QUERY request for MAINT at * has been accepted.
 DVHQRY3389I DATAMOVE DATAMOVE at SSI1 Status: INACTIVE WUCF:NONE
 DVHQRY3389I Pending: 0 Autologs: 0
 DVHREQ2289I Your QUERY request for MAINT at * has completed; with RC =
 DVHREQ2289I 0.
```

& As soon as your clone is complete, log onto LINUX02. The Linux
operating system loads.

& SSH to the new Linux virtual server and update the network
configuration files to make permanent the network changes. You might
need to modify the IP address and host name through a tool like yast2.

& From the command line execute:

```
# yast 
```

& Select Network Devices and Enter on Network Settings

![Select Network Devices and Enter on Network
Settings](/imgs/yast-net1.png){#fig:yast-net1}

& Press **alt+s** to go to "Hostname/DNS" tab, update the hostname of
the new server and press **alt+v** to go back to "Overview" tab.

![Change your hostname](/imgs/yast-net4.png){#fig:yast-net4}

& Back to "Overview" tab, select the network card using \"TAB\" key and
press Alt+i to edit it.

![Select the network card](/imgs/yast-net2.png){#fig:yast-net2}

& Go to IP Address Field and update it using the new IP

![Update the IP Address](/imgs/yast-net3.png){#fig:yast-net3}

& As soon as you change the IP address you will lose your SSH
connection. Reconnect using your new IP or reboot the server from 3270
console.

& Check that the Linux operating system reboots properly and that the
network connections are correct.

& Try pinging this Linux server or try to access a remote site from this
Linux server.

& Congratulations, your clone is complete!
:::

This is our actual environment after the changes:

![Complete Environment](/imgs/workshop-linux.png){#fig:completeenv}
