---
order: 45
icon: ":robot_face:"
---
# Basic system automation

It is useful to configure z/VM so that all production Linux systems, or
all Linux systems desired to be started, come up when z/VM is IPLed.
Contrarily, when z/VM is shut down, it is useful for all Linux systems
running to first come down cleanly. z/VM has a facility that allows a
privileged user to set a time parameter that defines the amount of time
reserved for guests to come down after the z/VM SHUTDOWN command is
issued, but before z/VM itself comes down. This value is used as the
default timeout interval for SIGNAL, FORCE, and SHUTDOWN commands when
no explicit interval is specified. If SET SIGNAL SHUTDOWN is not
specified in the system configuration file, the default is 0, which
suppresses guest shutdown signals. We have setup the SIGNAL SHUTDOWN to
500 seconds on page .

## Autolog Linux systems to boot at z/VM IPL

Like we did for TCPIP on section
[6.5.6](#sec:autologtcpip){reference-type="ref"
reference="sec:autologtcpip"} (page ), we will edit the PROFILE EXEC for
AUTOLOG1 to include our Linux guests.

Instead of login on AUTOLOG1, we will modify this file from MAINT,
linking AUTOLOG1 191 disk.

::: easylist
& Logon as MAINT

& Link to the AUTOLOG1 191 minidisk. Type this command and press the
Enter key:

```
link autolog1 191 091 mr
```

& Access the 091 minidisk as Z. Type this command and press the Enter
key:

```
access 091 z
```

& Edit AUTOLOG1's PROFILE EXEC.

```
xedit profile exec z
```

& Find Customer processing comment:

```
====> /customer
```

& Our previous update left this section like this:

```
...
/* Customer processing can be added here
*/
/*********************************************************************/
"CP XAUTOLOG TCPIP"   /* Autolog TCPIP */
"CP SET MDC STOR 0M 256M" /* Limit minidisk cache in CSTOR */
...
```

& Add your Linux guests LINUX1 and LINUX2:

```
...
/* Customer processing can be added here
*/
/*********************************************************************/
"CP XAUTOLOG TCPIP"   /* Autolog TCPIP */
"CP SET MDC STOR 0M 256M" /* Limit minidisk cache in CSTOR */
"CP XAUTOLOG LINUX1"   /* Autolog LINUX1 */
"CP XAUTOLOG LINUX2"   /* Autolog LINUX2 */
...
```

& Save and quit using **file**

& Detach the 091 minidisk (AUTOLOG1's 191). Type this command and press
the Enter key:

```
release z (det
```

& If you want to test the autolog procedure, shutdown your Linux guests
and from the command line type this command and press the Enter key:

```
xautolog autolog1
```
:::
