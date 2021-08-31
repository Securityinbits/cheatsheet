Setup


so-status -  to view if all services are running or not

To change IP



To view Ip for different interface, I may be wron. found this cmd from this discussion https://github.com/Security-Onion-Solutions/securityonion/discussions/2382
sudo salt-call pillar.get global
sudo salt-call grains.get ip_interfaces

https://docs.securityonion.net/en/2.3/ip.html
so-ip-update
