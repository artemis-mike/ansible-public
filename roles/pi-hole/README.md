# Pi-Hole
## Setup
```
play -i inventory.yaml -l pi roles/pi-hole/setup_pi-hole.yaml
```

# Know Issues
## Installing on Ubuntu or Fedora
(From https://github.com/pi-hole/docker-pi-hole#installing-on-ubuntu-or-fedora)  
Modern releases of Ubuntu (17.10+) and Fedora (33+) include `systemd-resolved` which is configured by default to implement a caching DNS stub resolver. This will prevent pi-hole from listening on port 53. The stub resolver should be disabled with: 
```
sudo sed -r -i.orig 's/#?DNSStubListener=yes/DNSStubListener=no/g' /etc/systemd/resolved.conf
```

This will not change the nameserver settings, which point to the stub resolver thus preventing DNS resolution. Change the `/etc/resolv.conf` symlink to point to `/run/systemd/resolve/resolv.conf`, which is automatically updated to follow the system's netplan: 
```
sudo sh -c 'rm /etc/resolv.conf && ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf'
```
After making these changes, you should restart systemd-resolved using systemctl restart systemd-resolved

## Pi-Hole and Fritz!Box
(From https://docs.pi-hole.net/routers/fritzbox-de/)  
Pi-Hole ~~can~~ should be set as DNS for
- [DHCP Clients](https://docs.pi-hole.net/routers/fritzbox-de/#pi-hole-als-dns-server-via-dhcp-an-clients-verteilen-lan-seite)
- [Fritzbox itself](https://docs.pi-hole.net/routers/fritzbox-de/#pi-hole-als-upstream-dns-server-der-fritzbox-wan-seite)

Setting Pi-Hole as DSN for the Fritz!Box itself and also providing an alternative, public DNS (like `9.9.9.9`).
