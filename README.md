# Ansible Role: HAProxy

Installs HAProxy on RedHat/CentOS and Debian/Ubuntu Linux servers. Forked from geerlingguy.haproxy and expanded some haproxy configuration.

**Note**: This role _officially_ supports HAProxy versions 1.4 or 1.5. Future versions may require some rework.

## Requirements

None.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

    haproxy_socket: /var/lib/haproxy/stats

The socket through which HAProxy can communicate (for admin purposes or statistics). To disable/remove this directive, set `haproxy_socket: ''` (an empty string).

    haproxy_chroot: /var/lib/haproxy

The jail directory where chroot() will be performed before dropping privileges. To disable/remove this directive, set `haproxy_chroot: ''` (an empty string). Only change this if you know what you're doing!

    haproxy_user: haproxy
    haproxy_group: haproxy

HAProxy defaults configuration example.
```
haproxy_defaults:
  - log global
  - mode http
  - option httplog
  - option dontlognull
  - timeout http-request    10s
  - timeout queue           1m
  - timeout connect         10s
  - timeout client          5m
  - timeout server          5m
  - timeout http-keep-alive 10s
  - timeout check           10s
```

HAProxy frontends configuration directives.

```
haproxy_frontend_servers:
  - name: frontend_server_example
    bind: "{{ ansible_host }}"
    mode: http
    port: 80
    options:
      - forwardfor
      - httpclose
    additional_options:
      - acl is_delete method DELETE
      - http-request deny if is_delete
    default_backend: backend_server_example

HAProxy backends configuration directives.
```

HAProxy backends configuration directives.
```
haproxy_backend_servers:
  - name: backend_server_example
    mode: http
    balance: roundrobin
    options:
      - forwardfor
      - httpclose
    servers:
      - name: server1
        address: 192.168.1.2:80
        check: weight 1 check inter 1000 rise 5 fall 1
      - name: server1
        address: 192.168.1.3:80
        check: weight 1 check inter 1000 rise 5 fall 1
      - name: server1
        address: 192.168.1.4:80
        check: weight 1 check inter 1000 rise 5 fall 1
```

A list of backend servers (name and address) to which HAProxy will distribute requests.

    haproxy_global_vars:
      - 'ssl-default-bind-ciphers ABCD+KLMJ:...'
      - 'ssl-default-bind-options no-sslv3'

A list of extra global variables to add to the global configuration section inside `haproxy.cfg`.

## Dependencies

None.

## Example Playbook

    - hosts: balancer
      sudo: yes
      roles:
        - { role: vdzhorov.haproxy }

## License

MIT / BSD

## Author Information

This role was created in 2015 by [Jeff Geerling](https://www.jeffgeerling.com/), author of [Ansible for DevOps](https://www.ansiblefordevops.com/).
