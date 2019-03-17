ryezone_labs.dhcp
=========

This role installs and configures DHCP on Ubuntu.  It has support for static and
dynamic addressing.

Requirements
------------

Requires two network interfaces on the machine.  One for the WAN and one for the
LAN.

Role Variables
--------------

| Variable Name | Type | Default Value | Description |
| ------------- | ---- | ------------- | ----------- |
| `dhcp_default_lease_time` | integer | `600` | Default lease time in seconds |
| `dhcp_max_lease_time ` | integer | `7200 ` | Maximum lease time in seconds |
| `dhcp_authoritative` | bool | `true` | Sets the DHCP server as authoritative |
| `dhcp_log_facility` | string | `local7` | Sets the logging mode for DHCP |
| `dhcp_subnets` | list of hashes | | List of subnets to serve DHCP  |
| `dhcp_hosts` | list of hashes | | List of hosts to statically assign IP addresses |

## dhcp_subnets Hash Structure

| Variable Name | Type | Description |
| ------------- | ---- | ----------- |
| `interface` | string | Interface to listen for DHCP Requests |
| `address` | string | Gateway Address |
| `network` | string | Network CIDR |
| `netmask` | string | Network netmask |
| `broadcast` | string | Network Broadcast Address |
| `range.start` | string | Start address of dynamically assigned IP addresses. |
| `range.end` | string | End address of dynamically assigned IP addresses. |

## dhcp_hosts Hash Structure

| Variable Name | Type | Description |
| ------------- | ---- | ----------- |
| `name` | string | name of the host being statically assigned |
| `mac_address` | string | mac address of the host being statically assigned |
| `fixed_address` | string | Static IP Address for the host |

## Global Role Variables

| Variable Name | Type | Default Value | Description |
| ------------- | ---- | ------------- | ----------- |
| `ryezone_labs_external_dns` | List of string | `['1.1.1.1', '1.0.0.1']` | List of external DNS servers to use. |
| `ryezone_labs_lanInterface` | string | `eth1` | Interface to serve DHCP |
| `ryezone_labs_top_level_domain` | string | `domain.local` | Top level domain for internal network. |

Example Playbook
----------------

```yaml
- hosts: 127.0.0.1
  connection: local
  vars:
    ryezone_labs_external_dns:
      - 1.1.1.1
      - 1.0.0.1
    ryezone_labs_lanInterface: eth1
    ryezone_labs_top_level_domain: domain.local
    dhcp_subnets:
      - interface: "{{ ryezone_labs_lanInterface }}"
        address: 10.100.10.1
        network: 10.100.10.0
        netmask: 255.255.255.0
        broadcast: 10.100.10.255
        range:
          start: 10.100.10.100
          end: 10.100.10.250
    dhcp_hosts:
      - name: router
        mac_address: 00:35:10:00:00:01
        fixed_address: 10.100.10.1
    dhcp_default_lease_time: 600  
    dhcp_max_lease_time: 7200
    dhcp_authoritative: true
    dhcp_log_facility: local7
  roles:
   - ryezone_labs.dhcp
```

License
-------

BSD
