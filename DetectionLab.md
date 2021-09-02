Rough notes:

Install suricata
https://suricata.readthedocs.io/en/latest/quickstart.html
replace eth0 with your interface, i did around 3 places

sudo systemctl restart suricata
sudo systemctl enable suricata

to test signature
wget http://testmynids.org/uid/index.html

To work as gateway need to enable net.ipv4.ip_forward = 1 to forward all packets
https://www.systutorials.com/setting-up-gateway-using-iptables-and-route-on-linux/

### Configure Ubuntu Machine to capture traffic from other machine on the network
This [guide](https://monoinfinito.wordpress.com/series/setting-up-a-linux-gatewayrouter-a-guide-for-non-network-admins/) is quiet good and give good overview of steps.

ens33 - WAN interface
ens38 - LAN interface 

1. In order to have the forwarding rules persisting after a reboot, we need first to change /etc/sysctl.conf to allow IP forwarding. It’s just a mater of uncommenting this line:
`net.ipv4.ip_forward = 1`

2. Setup iptables, more [info](https://help.ubuntu.com/community/IptablesHowTo)
  - `sudo iptables --table nat --append POSTROUTING --out-interface ens33 -j MASQUERADE`
  - `sudo iptables --append FORWARD --in-interface ens38 -j ACCEPT`
  -  Save iptable using `iptables-save`
  -  Persist iptable using any method mention  [here](https://www.thomas-krenn.com/en/wiki/Saving_Iptables_Firewall_Rules_Permanently) using iptables-persistent

3. Configure [bind](https://serverspace.io/support/help/configure-bind9-dns-server-on-ubuntu/) Server on Linux gateway


