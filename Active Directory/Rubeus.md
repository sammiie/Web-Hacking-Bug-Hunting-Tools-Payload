## Harvesting Tickets

A compiled copy of rubeus should already have been on the target machine

Navigate to the directory where you have **Rubeus** and use the command below;

`Rubeus.exe harvest /interval:30`

> This command tells Rubeus to harvest for TGTs every 30 seconds

![image](https://user-images.githubusercontent.com/16500435/103440167-ab932880-4c43-11eb-8313-d5fb2d6d79ab.png)


## Brute-Forcing / Password-Spraying

Rubeus can both brute force passwords as well as password spray user accounts. When brute-forcing passwords you use a single user account and a wordlist of passwords to see which password works for that given user account. In password spraying, you give a single password such as Password1 and "spray" against all found user accounts in the domain to find which one may have that password.

This attack will take a given Kerberos-based password and spray it against all found users and give a .kirbi ticket. This ticket is a TGT that can be used in order to get service tickets from the KDC as well as to be used in attacks like the pass the ticket attack.

Before password spraying with Rubeus, you need to add the domain controller domain name to the windows host file. You can add the IP and domain name to the hosts file from the machine by using the echo command: 

`echo 10.10.191.100 CONTROLLER.local >> C:\Windows\System32\drivers\etc\hosts`

**Password spraying** using password **Password1** we use the command 

`Rubeus.exe brute /password:Password1 /noticket`

![image](https://user-images.githubusercontent.com/16500435/103440287-7a672800-4c44-11eb-93c1-293160e888c8.png)

>This will take a given password and "spray" it against all found users then give the .kirbi TGT for that user 
