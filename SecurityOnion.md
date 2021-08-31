Setup


so-status -  to view if all services are running or not

To change IP



To view Ip for different interface and app. Found this cmd from this discussion https://github.com/Security-Onion-Solutions/securityonion/discussions/2382
sudo salt-call pillar.get global
sudo salt-call grains.get ip_interfaces

First reconfigured the required nic using change network settings
sudo SecurityOnion/setup/so-setup iso

Then used the below to update the IP address of management interface.
https://docs.securityonion.net/en/2.3/ip.html
so-ip-update
