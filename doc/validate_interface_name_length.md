# HLD Validate interface name length



####  Rev 0.1



# Table of Contents

​	-[Revision](#revision)

​	-[Motivation](#motivation)

​	-[About this Manual](#about-this-manual)

​	-[Design](#design)

​	-[Tests](#tests)


# Revision
| Rev  |   Date   |    Author    | Change Description |
| :--: | :------: | :----------: | ------------------ |
| 0.1  | 10/05/23 | Eden Grisaro | Initial version    |



# Motivation

The kernel can't configure interfaces with names that are longer than `IFNAMSIZ` (`IFNAMSIZ` is the maximum buffer size needed to hold an interface name, including its terminating zero byte.). Since we have this limitation we want to add validation to the SONiC to check the interface's name size.



# About this Manual

This document provides an overview of the implementation of adding validation of the interface's name size.


# Design

## Add validation to the following CLI command

These are the interfaces that the is user able to config:
* Vxlan
* Vlan
* Vrf
* Loopback
* Subinterface
* Portchannel

For each one, we will add (or make sure we have) validation for the length of the interface name.

The following interfaces don't have validation of the interface name in their CLI command therefore we will add one:

### Vxlan
The user can configure Vxlan via CLI command, for example: "config vxlan map add neighbor_adv 10 1". We will add a limitation in the CLI command that the vxlan_name length will not be greater than `IFNAMSIZ`. The validation will be added to the sonic-utilities/config/vxlan.py file in the "add_vxlan" function.
```python
if len(vxlan_name) > 15:
        ctx.fail("'vxlan_name' is too long!")
```

### Subinterface
The user can configure Subinterface via CLI command, for example: "sudo config subinterface add Ethernet0.100". We will add a limitation in the CLI command that the subinterface_name length will not be greater than `IFNAMSIZ`. The validation will be added to the sonic-utilities/config/main.py file in the "add_subinterface" function.
```python
if len(subinterface_name) > 15:
        ctx.fail("'subinterface_name' is too long!")
```


The following interface already has validation and we don't need to add an additional check:
### Vlan
The user can configure vlan via CLI command, for example: "config vlan add 100". Valid vlan name is "Vlan"+{vid}. There is a limitation in the CLI command that the vid will not be greater than 4096. This limitation promises us the vlan name length will not cross the `IFNAMSIZ`. (You can find the length validation in the file sonic-utilities/config/vlan.py in the "add_vlan" function).

### Vrf
The user can configure vrf via CLI command, for example: "config vrf add Vrf100". Valid vrf name must begin with 'Vrf' or named 'mgmt'/'management' in case of ManagementVRF. There is a limitation in the CLI command that the vrf name length will be less than 16. This limitation promises us the vlan name length will not cross the `IFNAMSIZ`. (You can find the length validation in the file sonic-utilities/config/main.py in the "add_vrf" function).

### Loopback
The user can configure loopback via CLI command, for example: "sudo config loopback add Loopback11". There is a limitation in the CLI command that the loopback name length will be less than 11. This limitation promises us the vlan name length will not cross the `IFNAMSIZ`. (You can find the length validation in the file sonic-utilities/config/main.py in the "is_loopback_name_valid" function).

### Portchannel

The user can configure portchannel via CLI command, for example: "sudo config portchannel add PortChannel0011". There is a limitation in the CLI command that the portchannel name length will be less than 16. This limitation promises us the vlan name length will not cross the `IFNAMSIZ`. (You can find the length validation in the file sonic-utilities/config/main.py in the "is_portchannel_name_valid" function).


interface | file path | function name | need to add validation? | example for CLI command |
--------- | --------- | ------------- | ----------------------- |------------------------ |
Vxlan | sonic-utilities/config/vxlan.py | add_vxlan | yes | "config vxlan map add neighbor_adv 10 1"
Vlan | sonic-utilities/config/vlan.py | add_vlan | no | "config vlan add 100"
Vrf | sonic-utilities/config/main.py | add_vrf | no | "config vrf add Vrf100"
Loopback | sonic-utilities/config/main.py | is_loopback_name_valid | no | "sudo config loopback add Loopback11"
Subinterface | sonic-utilities/config/main.py | add_subinterface | yes | "sudo config subinterface add Ethernet0.100"
Portchannel | sonic-utilities/config/main.py | is_portchannel_name_valid | no | "sudo config portchannel add PortChannel0011"

## Add validation to the Yang model

The following interfaces don't have validation of the interface name in their Yang therefore we will add one: 
### Vxlan
In the sonic-yang-models/yang-models/sonic-vxlan.yang we will add validation to the leaf name to limit the name length.

### Subinterface
In the sonic-yang-models/yang-models/sonic-vlan-sub-interface.yang we will add validation to the leaf name to limit the name length.


The following interface already has validation and we don't need to add additional checks:
### Vlan
In the sonic-yang-models/yang-models/sonic-vlan.yang we have validation to the leaf name.

### Vrf
In the sonic-yang-models/yang-models/sonic-vrf.yang we have validation to the leaf name.

### Loopback
In the sonic-yang-models/yang-models/sonic-loopback-interface.yang we have validation to the leaf name.

### Portchannel
In the sonic-yang-models/yang-models/sonic-portchannel.yang we have validation to the leaf name.


interface | file path | need to add validation? |
--- | --- | --- | --- |--- |--- |--- |--- |--- |--- |--- |---
Vxlan | sonic-yang-models/yang-models/sonic-vxlan.yang | yes | 
Vlan | sonic-yang-models/yang-models/sonic-vlan.yang | no |
Vrf | sonic-yang-models/yang-models/sonic-vrf.yang | no | 
Loopback | sonic-yang-models/yang-models/sonic-loopback-interface.yang | no |
Subinterface | sonic-yang-models/yang-models/sonic-vlan-sub-interface.yang | yes | 
Portchannel | sonic-yang-models/yang-models/sonic-portchannel.yang | no |

# Tests
## UT in the sonic-utilities:
The following interfaces don't have unit test cases that check the configuration of the interface with too long interface name therefore we will add one: 
### Vxlan
We will add a test case to the "test_config_vxlan_add" that will try to configure vxlan with a too long name, and it will expect to fail.

### Vlan
We will add a test case to the "test_config_vlan_add_member_with_invalid_vlanid" that will try to configure vlan with a too long name, and it will expect to fail.

### Vrf
We will add a test case to the "test_invalid_vrf_name" that will try to configure vrf with a too long name, and it will expect to fail.

### Portchannel
We will add a test case to the "test_add_portchannel_with_invalid_name_adhoc_validation" that will try to configure portchannel with a too long name, and it will expect to fail.


The following interface allready has unit test case that check configuration of interface with a too long interface name and we don't need to add aditional test:
### Subinterface
In the test "test_invalid_subintf_creation" we already has test case with a too long name ("Ethernet1000.102) that except to fail.

interface | file path | test name | need to add test? |
--- | --- | --- | --- |--- |--- |--- |--- |--- |--- |--- |---
Vxlan | sonic-utilities/tests/vxlan_test.py | test_config_vxlan_add |yes | 
Vlan | sonic-utilities/tests/vlan_test.py | test_config_vlan_add_member_with_invalid_vlanid | yes |
Vrf | sonic-utilities/tests/vrf_test.py | test_invalid_vrf_name | yes | 
Loopback | sonic-utilities/tests/config_test.py | test_add_loopback_with_invalid_name_adhoc_validation | yes |
Subinterface | sonic-utilities/tests/subintf_test.py | test_invalid_subintf_creation | no | 
Portchannel | sonic-utilities/tests/portchannel_test.py | test_add_portchannel_with_invalid_name_adhoc_validation | yes |

## Yang tests:

The following interfaces don't has a Yang test that check configuration of interface with a too long interface name therefore we will add one: 

### Vxlan
### Vlan
### Vrf
### Loopback


The following interface already has a Yang test that check configuration of interface a with too long interface name and we don't need to add aditional test:


### Subinterface
### Portchannel

interface | file path | need to add validation? |
--- | --- | --- | --- |--- |--- |--- |--- |--- |--- |--- |---
Vxlan | sonic-yang-models/tests/yang_models_tests/tests/vxlan.json <br /> sonic-yang-models/tests/yang_models_tests/config_tests/vxlan.json | yes | 
Vlan | sonic-yang-models/tests/yang_models_tests/tests/vlan.json <br /> sonic-yang-models/tests/yang_models_tests/config_tests/vlan.json | yes |
Vrf | sonic-yang-models/tests/yang_models_tests/tests/vrf.json <br /> sonic-yang-models/tests/yang_models_tests/config_tests/vrf.json | yes | 
Loopback | sonic-yang-models/tests/yang_models_tests/tests/loopback.json <br /> sonic-yang-models/tests/yang_models_tests/config_tests/loopback.json | yes |
Subinterface | sonic-yang-models/tests/yang_models_tests/tests/vlan_sub_interface.json <br /> sonic-yang-models/tests/yang_models_tests/config_tests/vlan_sub_interface.json | no | 
Portchannel | sonic-yang-models/tests/yang_models_tests/tests/portchannel.json <br /> sonic-yang-models/tests/yang_models_tests/config_tests/portchannel.json | no |