# Switch Date On "Show Version" CLI Command

# High-Level Design Document



####  Rev 0.1



# 1 Table of Contents - TODO Update

[toc]

# 2 List of Figures
* [flow diagram](#9-Flow)

# 3 Revision
| Rev  |   Date   |    Author    | Change Description |
| :--: | :------: | :----------: | ------------------ |
| 0.1  | 02/22/22 | Eden Grisaro | Initial version    |



# 4 Motivation

The date of the system is a key for debug process. Nowadays, SONiC does not provide a date in the techsupport, which makes the debug process be more difficult. 



# 5 About this Manual

This document provides an overview of the implementation to add a new row which provides the <u>current</u> date on the switch on the 'show version' CLI command.



before:

```
admin@sonic:~$ show version
SONiC Software Version: SONiC.HEAD.32-21ea29a
Distribution: Debian 9.8
Kernel: 4.9.0-8-amd64
Build commit: 21ea29a
Build date: Fri Mar 22 01:55:48 UTC 2019
Built by: johnar@jenkins-worker-4

Platform: x86_64-mlnx_msn2700-r0
HwSKU: Mellanox-SN2700
ASIC: mellanox
ASIC Count: 1
Serial Number: MT1822K07815
Model Number: MSN2700-CS2FO
Hardware Rev: A1
Uptime: 14:40:15 up 3 min,  1 user,  load average: 1.26, 1.45, 0.66
```

after:

```
admin@sonic:~$ show version
SONiC Software Version: SONiC.HEAD.32-21ea29a
Distribution: Debian 9.8
Kernel: 4.9.0-8-amd64
Build commit: 21ea29a
Build date: Fri Mar 22 01:55:48 UTC 2019
Built by: johnar@jenkins-worker-4

Platform: x86_64-mlnx_msn2700-r0
HwSKU: Mellanox-SN2700
ASIC: mellanox
ASIC Count: 1
Serial Number: MT1822K07815
Model Number: MSN2700-CS2FO
Hardware Rev: A1
Uptime: 14:40:15 up 3 min,  1 user,  load average: 1.26, 1.45, 0.66
Date: Tue 22 Feb 2022
```



# 6 Design

In sonic-utilities, in the "version" subcommand, print of the current date will be added using date from the datetime library.



# 7 Build and runtime dependencies---??? not sure about this

To get the current date on the switch, we import the date from the datetime library.



# 8 CLI

This command displays the "show version" output and in addition, the current date in a new row. 

Usage:
```
show version
```
Example:
```
admin@sonic:~$ show version
SONiC Software Version: SONiC.HEAD.32-21ea29a
Distribution: Debian 9.8
Kernel: 4.9.0-8-amd64
Build commit: 21ea29a
Build date: Fri Mar 22 01:55:48 UTC 2019
Built by: johnar@jenkins-worker-4

Platform: x86_64-mlnx_msn2700-r0
HwSKU: Mellanox-SN2700
ASIC: mellanox
ASIC Count: 1
Serial Number: MT1822K07815
Model Number: MSN2700-CS2FO
Hardware Rev: A1
Uptime: 14:40:15 up 3 min,  1 user,  load average: 1.26, 1.45, 0.66
Date: Tue 22 Feb 2022
```

# 9 Flow

![](C:\code\SONiC\doc\show version addDate temp\flow.drawio.png)
