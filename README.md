
# Information
This is a docker container running OpenVPN client. It has the possibility to link other containers to send traffic trough openvpn.
You can also specify which ports that should be accessible from vpn to OpenVPN client container.

If the vpn connection is down only ping, dns and the OpenVPN Remote ports will be allowed from OpenVPN client container to outside.
----------
# Environment Variables
## Description
- LOCAL_NETWORKS: Comma separated list of subnets that you would like to grant full access to from container
- OPENVPN_REMOTE_PORTS: Comma separated list of ports that your openvpn server is listening on
- OPENVPN_FIREWALL_ALLOW_TCP: Comma separated list of TCP ports that you want to open to container, if environment variable is empty all TCP ports will be allowed from outside. If not specified, TCP traffic from outside will be blocked.
- OPENVPN_FIREWALL_ALLOW_UDP: Comma separated list of UDP ports that you want to open to container, if environment variable is empty all UDP ports will be allowed from outside. If not specified, UDP traffic from outside will be blocked.
## Examples
- LOCAL_NETWORKS=192.168.0.0/24,192.168.1.0/24
- OPENVPN_REMOTE_PORTS=1194,1195
- OPENVPN_FIREWALL_ALLOW_TCP=80,443
- OPENVPN_FIREWALL_ALLOW_UDP=53


----------
# Start OpenVPN client container:
```sh
$ docker run -dt \
    --name openvpn-client \
    --hostname openvpn-client \
    -v /data/vpn:/vpn \
    -e OPENVPN_REMOTE_PORTS=1194,1195\
    -e OPENVPN_FIREWALL_ALLOW_TCP=80,443 \
    -e OPENVPN_FIREWALL_ALLOW_UDP=80,443 \
    -e LOCAL_NETWORKS=192.168.0.0/24 \
    --cap-add=NET_ADMIN \
    --device /dev/net/tun \
    robostlund/openvpn-client:latest
```

## Publish local services to openvpn client container
Lets say you would like to run a webserver that is accessible from the openvpn ip you can link the webserver container to the openvpn client container.

```sh
$ docker run -dt \
    --name webserver \
    --hostname webserver \
    --net container:openvpn-client \
    -v /data/web:/data \
    robostlund/nginx-php-mysql-static:latest
```