# What is this?
A container that runs unbound and openconnect(with a SOCKS5 proxy).

NOTE: Privileged mode is needed for the TUN adapter in the container.

Note 2: I require TOTP token and it's easier to just run this as an interactive shell

This is based on https://github.com/Gibby/dockerfiles/tree/master/openconnect

After unbound starts up(if UNBOUND is set to true), it overwrites /etc/resolv.conf in the container to use 127.0.0.1

## Environment variables needed
UNBOUND - true (To run unbound in container also)
PASSWORD - The password passed to openconnect.

OPTIONS - Options passed to openconnect. Example -e OPTIONS="-u awesome_admin --authgroup=ADMINS --no-cert-check"

SERVER - Server to connect to with openconnect.

## File mount
custom.conf for Unbound.

## Docker run options needed
And a port for the socks 5 proxy
-p 127.0.0.1:9000:9000

## Example 1 password
```
docker run -it --rm --privileged \
-p 127.0.0.1:9000:9000 \
-v custom.conf:/etc/unbound/unbound.conf.d/custom.conf \
-e OPTIONS="-u awesome_admin --authgroup=ADMINS --no-cert-check" \
-e SERVER=vpnserver.local \
-t zipleen/openconnect-it
```

## To also expose Unbound (DNS) add
-p 127.0.0.1:53:53/tcp \
-p 127.0.0.1:53:53/udp \

## SSH Config on localhost
Put something like below in your .ssh/config with ProxyCommand for the hosts behind the vpn

ProxyCommand ssh -p 9000 root@localhost nc %h %p

Use firefox or any browser and set a socks proxy for localhost:9000 and access the internet throught the vpn.

## TODO
Add instructions for using redsocks on linux with iptables to route networks through iptables to redsocks to the socks 5 proxy on the container
