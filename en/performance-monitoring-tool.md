---
order: 35
icon: ":bar_chart:"
---
# Performance Monitoring Tools

## The z/VM Performance Toolkit

The Performance Toolkit for z/VM is a global performance product that
provides real-time monitoring functions and historical performance
analysis for multiple z/VM systems (local or remote). In this section
you will learn how to enable the Performance Toolkit. You will go over
later in the class how to use the Performance Toolkit for understanding
basic system performance.

::: easylist
& Open your web browser to the z/VM IP address and port number for
Performance Toolkit for VM.

    http://<ip_address>

![Performance Toolkit](/imgs/perftk1.png){#fig:perftk1}

& On the Web Server Login screen, type your user ID and password (maint
userid).

![Performance Toolkit login](/imgs/perftk2.png){#fig:perftk2}

& Click the name of your z/VM host system. You see a screen like the
following:

![Performance Toolkit z/VM host](/imgs/perftk3.png){#fig:perftk3}

Each item show a different perspective of your environment. Try it.
:::

## Monitoring Linux

## z/VM Monitor stream - APPLDATA Support

This exercise describes how to load those components of the support that
have been compiled as separate modules and how to set up your VM guest
for the APPLDATA record support.

::: easylist
& Login to LINUX1 as root

& Load the data gathering modules:

```
# modprobe appldata_mem
# modprobe appldata_os
# modprobe appldata_net_sum
```

& APPLDATA monitor records are produced if both a particular data
gathering module and the monitoring support in general are switched on.
To switch on monitoring, use the echo command to send a non-zero value
into the monitoring variables in the /proc/ virtual file system:

```
# echo 1 > /proc/sys/appldata/timer
# echo 1 > /proc/sys/appldata/mem
# echo 1 > /proc/sys/appldata/os
# echo 1 > /proc/sys/appldata/net_sum
```

& You can set the time that lapse between consecutive data samples. The
time you set is measured by the virtual CPU timer. Because the virtual
timer slows down as the guest idles, the time sampling interval in real
time can be considerably longer than the value you set. The value in
/proc/sys/appldata/interval is the sample interval in milliseconds. The
default sample interval is 10000 ms.

To read the current value, issue:

```
# cat /proc/sys/appldata/interval 
```

& If you want to make this enabled permanently add the commands below to
your /etc/init.d/boot.local:

```
modprobe appldata_mem
modprobe appldata_os
modprobe appldata_net_sum
echo 1 > /proc/sys/appldata/timer
echo 1 > /proc/sys/appldata/mem
echo 1 > /proc/sys/appldata/os
echo 1 > /proc/sys/appldata/net_sum
```

& Now under "User Data" section you should be able to see section "29.
Linux systems\*" enabled.
:::
