# Ubuntu Based Server (new system)

## Change IP Address
Edit the config file and restart the network stack 
(NOTE: If you do this wrong, you may loose network connection - make sure you can get to the system physically or via your virtualization console if it goes wrong)

```sudo nano /etc/netplan/01-netcfg.yml```
- Change address/gateway/nameservers as needed
ctrl+O to save, ctrl+X to exit

Restart the network services (or reboot the host)

```sudo systemctl restart systemd-networkd```




# OpenSuse Based Server (old system)
## Change IP Address

Use yast to modify network settings. Make sure you tab through screens to set the netmask, gateway, and name servers too!
