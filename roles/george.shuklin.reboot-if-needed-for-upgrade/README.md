reboot-if-needed-for-upgrade
=========

This playbook reboots server and waits for it to come back online.
By default reboots only if reboot operation is pending due to apt-get upgrade operation (/var/run/reboot-required)

Requirements
------------

Server should be accessible from ansible machine via direct TCP by it IP.

Changelog
---------
I significantly reworked it to work around nasty SSH errors: `Failed to connect to the host via ssh: Shared connection to 192.168.0.2 closed.`, now it uses `ansible_default_ipv4.address`.


Role Variables
--------------

Role checks if reboot is pending, reboots server and waiting until it port (default 22, ssh) is available.

It will works only if you have direct access to that server. Normally `ansible_default_ipv4.address` is used to check if server is online. If you have trouble to get external (white) IP from instance, checkout get-external-ip-via-dyndns role.


Variables:
- `ip_to_probe`: ip address to wait (default to `ansible_default_ipv4.address`, which is gathered by setup module)
- `port_to_probe`: port to probe (default 22, ssh)
- `delay_before_probe`: delay after reboot command (default 10)
- `probe_timeout`: max waiting time (if `ip_to_probe`:`port_to_probe` is not replying in `probe_timeout` time, role fails. default 70 seconds)
- `force_reboot`: Reboot regardless of /var/run/reboot-required (default: False)
- `reboot_delay`: Delay before issuing reboot command (default: 2 seconds), to help ssh to process command


Example Playbook
----------------

Simple:
```
- hosts: servers
  vars:
    probe_timeout: 360
  roles:
     - { role: reboot-if-needed-for-upgrade }
```
Tricky:
```
- hosts: servers
  vars:
    port_to_probe: 2222
    ip_to_probe: "{{external_ip}}"
    delay_before_probe: 120
    probe_timeout: 360
    force_reboot: True
  role:
    - reboot-if-needed-for-upgrade
```

License
-------

BSD

Author Information
------------------
(C) 2016-2017, servers.com, by George Shuklin
