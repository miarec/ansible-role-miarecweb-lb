Role to install HAProxy load balancer

## Features

This role does the following 

1. Installs HAProxy 
2. Copies SSL certificate to the server if provided
3. Configured backend servers

## Role Variables

### SSL certificate file

    haproxy_ssl_cert_local_file: ./mycert.pem

Path to SSL certificate file (on Ansible controller), which will be copied to HAProxy host.

HAProxy requires that private key, certificate file and chain file are concatenanted into one file.

A self-signed certificate (for testing purposes) could be generated with:

    openssl req -new -newkey rsa:2048 -days 3650 -nodes -x509 -keyout server.key -out server.crt  '/CN=localhost'

    cat server.key server.crt >> mycert.pem

### Backend servers

    haproxy_backend_servers:
      - name: web1
        address: 10.0.0.1:443
        backup: False
      - name: web2
        address: 10.0.0.2:443
        backup: False
      - name: web3
        address: 10.0.0.3:443
        backup: True
    
In the above, example, we have 3 servers. `web1` and `web2` are primary servers with round-robin load-balancing of traffic.
`web3` is a backup servers, which is used when neither of primary servers are available.


### HTTP check URL

    haproxy_backend_httpchk: /status

URL to check a status of a backend server. HAProxy periodically checks this URL to verify if server is up.

## Dependencies

None.

## Example Playbook

    - hosts: lb
    
      vars:
        haproxy_backend_servers:
            - name: web1
            address: 10.0.0.1:443
            backup: False
            - name: web2
            address: 10.0.0.2:443
            backup: True
    
      roles:
        - ansible-role-miarecweb-lb