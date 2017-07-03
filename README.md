OULibraries.nginx
=========

Nginx webserver for OU Libraries. Uses official nginx stable repository.

You probably want to use this in conjuction [OULibraries.tls-cert](https://github.com/OULibraries/ansible-role-tls-cert)

Requirements
------------

* CentOS 7.x
* ansible >= 2.2

Role Variables
--------------

You'll need to define hostnames, backend servers, and an optional robots.txt overlay, eg.
```
nginx_sites:
 - name: example.com
   upstreams:
     - name: example-dev
       servers:
         - 192.168.1.10:443
         - 192.168.1.11:443
         - 192.168.1.12:443
   robots: disallow
 - name: dspace.example.com
   upstreams:
     - name: dspace-dev
       servers:
         - 192.168.1.13:8443
   robots: dspace
 - name: 1.example.com
   redirect_locations: # this is optional
     - src: /source
       dest: /destination
       code: 302
   upstreams:
     - name: 1-example-dev
       servers:
         - 192.168.1.13:9443
         - 192.168.1.14:9445
         - 192.168.1.15:9955
       locations:
         - /
   robots: disallow
 - name: 1.example.com
   include: No # this is optional. Will deploy just enough server to respond to acme challenges and redirect http -> https. Server config for https is up to somebody else.
   cert_name: example.com # this is optional. use if this site name is configured as a SAN for a certificate named after another site.
```

All backends must use SSL regardless of port.

See defaults/main.yml for the rest

Dependencies
------------


Example Playbook
----------------
An example vagrant playbook.

```
- hosts: localhost
  roles:
    - OULibraries.tls-cert

- hosts: nginx.vagrant.local
  become: true
  vars_files:
  pre_tasks:
    - copy:
        src: /vagrant/dhparam.pem
        dest: "{{ nginx_cert_path }}/dhparam.pem"
  roles:
    - OULibraries.nginx
```

This example has a pretask that copies over static DH parameters to speed provisioning.  This is fine for a test environment, but not suitable for production.

License
-------

[MIT](LICENSE)

Author Information
------------------

Jason Sherman
