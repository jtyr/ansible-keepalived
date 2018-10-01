keepalived
==========

Ansible role which helps to install and configure Keepalived.

The configuration of the role is done in such way that it should not be
necessary to change the role for any kind of configuration. All can be
done either by changing role parameters or by declaring completely new
configuration as a variable. That makes this role absolutely
universal. See the examples below for more details.

Please report any issues or send PR.


Examples
--------

```yaml
---

- name: Example of the default usage
  hosts: all
  roles:
    - keepalived

- name: Example of how to add vrrp_instance
  hosts: all
  vars:
    # Keep global_defs empty and just add vrrp_instance
    keepalived_config__custom:
      - vrrp_instance test:
          - state MASTER
          - interface eth1
          - virtual_router_id 123
          - priority {{ 255 - ansible_eth1.ipv4.address | regex_replace('^.*\.') | int }}
          - advert_int 1
          - virtual_ipaddress:
              - 192.168.101.200/24
    # Disable detailed logging in the sysconfig file
    keepalived_sysconfig_options: ""
  roles:
    - keepalived

- name: Example of how to write the config from scratch
  hosts: all
  vars:
    keepalived_config:
      - global_defs:
          - notification_email:
              - root@mydomain.com
          - notification_email_from host1@example.com
          - smtp_server localhost
          - smtp_connect_timeout 30
      - vrrp_instance test:
          - state MASTER
          - interface eth1
          - virtual_router_id 123
          - priority {{ 255 - ansible_eth1.ipv4.address | regex_replace('^.*\.') | int }}
          - advert_int 1
          - virtual_ipaddress:
              - 192.168.101.200/24
  roles:
    - keepalived
```


Role variables
--------------

Variables used by the role:

```yaml
# Package to be installed (explicite version can be specified here)
keepalived_pkg: keepalived

# Service name
keepalived_service: keepalived

# Path to the keepalived config file
keepalived_config_path: /etc/keepalived/keepalived.conf


# Default config
keepalived_config__default:
  - global_defs: []

# Custom config
keepalived_config__custom: []

# Final config
keepalived_config: "{{
  keepalived_config__default +
  keepalived_config__custom }}"


# Sysconfig path
keepalived_sysconfig_path: /etc/sysconfig/{{ keepalived_service }}

# Default value of the sysconfig options
keepalived_sysconfig_options: "-D"

# Default sysconfig options
keepalived_sysconfig__default:
  keepalived_options: "{{ keepalived_sysconfig_options }}"

# Custom sysconfig options
keepalived_sysconfig__custom: {}

# Default sysconfig content (see README for examples)
keepalived_sysconfig: "{{
  keepalived_sysconfig__default | combine(
  keepalived_sysconfig__custom) }}"


# Default path
keepalived_default_path: /etc/default/{{ keepalived_service }}

# Default content (see README for examples)
keepalived_default: {}
```


Dependencies
------------

- [`config_encoder_filters`](https://github.com/jtyr/ansible-config_encoder_filters)


License
-------

MIT


Author
------

Jiri Tyr
