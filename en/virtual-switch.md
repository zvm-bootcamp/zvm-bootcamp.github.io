---
order: 70
icon: server
---
# Virtual Switch

### Setting up a virtual switch (VSWITCH) with failover

In this exercise, we will setup the Virtual Switch to use two
controllers and two separate sets of OSA devices. If the primary
controller fails, the backup controller will take over. Likewise, if the
primary OSA fails, traffic will be switched to use the backup OSA
devices.

The high-level steps in the process are in the sections that follow:

::: easylist
& Define a VSWITCH & Shutdown and re-IPL z/VM
:::

Define the Virtual Switch in the SYSTEM CONFIG file to ensure the
Virtual Switch is defined on IPL. Use the MODIFY VSWITCH statement to
authorize a z/VM user to attach to the Virtual Switch.

::: easylist
& Log on as MAINT. & Display a list of available OSA devices. You should
see devices 803-805 and 806-808 free.

```
===> q osa free
```

Now you can execute the GETCF0 script to get write access to the primary
parm disk.

```
===> getcf0
```

& Edit the SYSTEM CONFIG file.

```
===> x system config x 
```

& Add the following statements to the end of the file to:

(Change the X in macprefix to your student number)

```
/* define vswitch named VSW1 and set MAC address prefix to 02-00-0X */
DEFINE VSWITCH VSW1 RDEV 803 806 
VMLAN MACPREFIX 0200(*|\textcolor{red}{XX}|*)
```

& Save the changes end exit the file.

```
===> file
```

& Check the syntax by using the CPSYNTAX command.

```
===> access 193 f
===> cpsyntax system config 
```

& Execute the script to release write access to the primary parm disk.

```
===> freecf0 
```
:::

### Test VSWITCH configuration

Restart the z/VM system and verify the changes you have made in the
networking section.

::: easylist
& Shutdown and re-ipl your z/VM system.

```
===> shutdown reipl 
```

& Log on as MAINT.

& Display the VSWITCH:

```
===> q vswitch 
```

Is the VSWITCH VSW1 defined?

& To see the status of the VSWITCH controllers, issue the command:

```
===> q controller
```

Which controller is your primary?
:::

If any of the above commands indicated you have problems in your setup,
review your work and try again. You will test the failover capability of
the VSWITCH in a later exercise.

This is our actual environment after the changes:

![Environment with TCPIP and
VSWITCH](/imgs/workshop-vswitch.png){#fig:workshopvswitch}
