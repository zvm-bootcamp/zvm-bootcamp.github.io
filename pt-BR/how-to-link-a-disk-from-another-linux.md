---
order: 25
icon: ":floppy_disk:"
---
# How to link a disk from another Linux

::: easylist
& To link a disk as Read-write from another server you need to make sure
that this server is down. You can check via 3270 or Linux:

```
linux1:~ # vmcp q linux2
HCPCQU045E LINUX2 not logged on
Error: non-zero CP response for command 'Q LINUX2': #45
```

& To link the disk we need to know the password of it, if you don't know
the password you can check its directory

```
===> DIRM FOR LINUX2 GET
```

& Check the "MULTIPLE" password and unlock the directory again. If the
password is not set, set it.

```
===> DIRM FOR LINUX2 UNLOCK
```

& You can link the disk using vmcp:

```
linux1:~ # vmcp link linux2 100 1100 mr multi
```

& To check execute:

```
linux1:~ # vmcp q dasd
```

& As soon as you link the disk, enable it

```
linux1:~ # chccwdev -e 1100
Setting device 0.0.1100 online
Done
```

& Check the dasd name using lsdasd:

```
linux1:~ # lsdasd 
Bus-ID     Status      Name      Device  Type  BlkSz  Size      Blocks
==============================================================================
0.0.0100   active      dasda     94:0    ECKD  4096   1125MB    288000
0.0.0101   active      dasdb     94:4    ECKD  4096   457MB     117000
0.0.0102   active      dasdc     94:8    ECKD  4096   4230MB    1082880
0.0.0103   active      dasdd     94:12   ECKD  4096   457MB     117000
0.0.0104   active      dasde     94:16   ECKD  4096   562MB     144000
0.0.0105   active      dasdf     94:20   ECKD  4096   562MB     144000
0.0.1100   active      dasdg     94:24   ECKD  4096   1125MB    288000


& In this case dasdg. Mount it on /mnt:

\begin{minted}{REXX}
mount /dev/dasdg1 /mnt/
```

& List /mnt

```
linux1:~ # ls /mnt
bin  boot  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  sbin  srv  success  sys  tmp  usr  var
linux1:~ # 
```

& As you can see all files from "root" filesystem of LINUX2 are
available, edit whatever you need and to free the disk again follow the
steps below:

&& umount the filesystem

```
linux1:~ # umount /mnt
```

&& Deactivate the disk:

```
linux1:~ # chccwdev -d 0.0.1100
```

&& Detach the disk

```
linux1:~ # vmcp det 1100
```

& Activate LINUX2 again

```
===> XAUTOLOG LINUX2
```
:::
