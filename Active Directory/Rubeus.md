## Harvesting Tickets

A compiled copy of rubeus should already have been on the target machine

Navigate to the directory where you have **Rubeus** and use the command below;

`Rubeus.exe harvest /interval:30`

> This command tells Rubeus to harvest for TGTs every 30 seconds

![image](https://user-images.githubusercontent.com/16500435/103440167-ab932880-4c43-11eb-8313-d5fb2d6d79ab.png)


## Brute-Forcing / Password-Spraying

>Rubeus can both brute force passwords as well as password spray user accounts. When brute-forcing passwords you use a single user account and a wordlist of passwords to see which password works for that given user account. In password spraying, you give a single password such as Password1 and "spray" against all found user accounts in the domain to find which one may have that password.

>This attack will take a given Kerberos-based password and spray it against all found users and give a .kirbi ticket. This ticket is a TGT that can be used in order to get service tickets from the KDC as well as to be used in attacks like the pass the ticket attack.

- [ ] Before password spraying with Rubeus, you need to add the domain controller domain name to the windows host file. You can add the IP and domain name to the hosts file from the machine by using the echo command: 

`echo 10.10.191.100 CONTROLLER.local >> C:\Windows\System32\drivers\etc\hosts`

**Password spraying** using password **Password1** we use the command 

`Rubeus.exe brute /password:Password1 /noticket`

![image](https://user-images.githubusercontent.com/16500435/103440287-7a672800-4c44-11eb-93c1-293160e888c8.png)

>This will take a given password and "spray" it against all found users then give the .kirbi TGT for that user 

## Kerberoasting

>Kerberoasting allows a user to request a **service ticket for any service with a registered SPN** then use that ticket to crack the service password. If the service has a registered SPN then it can be Kerberoastable however the success of the attack depends on how strong the password is and if it is trackable as well as the privileges of the cracked service account. To enumerate Kerberoastable accounts I would suggest a tool like BloodHound to find all Kerberoastable accounts, it will allow you to see what kind of accounts you can kerberoast if they are domain admins, and what kind of connections they have to the rest of the domain.

**Command**

`Rubeus.exe kerberoast`

>This will dump the Kerberos hash of any kerberoastable users

The hashes can be cracked using `hashcat`

`root@kali:~/Desktop/thm# hashcat -m 13100 -a 0 hash.txt pass.txt`

**What Can a Service Account do?**

After cracking the service account password there are various ways of exfiltrating data or collecting loot depending on whether the service account is a domain admin or not. If the service account is a domain admin you have control similar to that of a golden/silver ticket and can now gather loot such as dumping the NTDS.dit. If the service account is not a domain admin you can use it to log into other systems and pivot or escalate or you can use that cracked password to spray against other service and domain admin accounts; many companies may reuse the same or similar passwords for their service or domain admin users. If you are in a professional pen test be aware of how the company wants you to show risk most of the time they don't want you to exfiltrate data and will set a goal or process for you to get in order to show risk inside of the assessment.


## AS-REP Roasting (Dumping KRBASREP5 Hashes)

>Very similar to Kerberoasting, AS-REP Roasting dumps the krbasrep5 hashes of user accounts that have Kerberos pre-authentication disabled. Unlike Kerberoasting these users do not have to be service accounts the only requirement to be able to AS-REP roast a user is the user must have pre-authentication disabled.

>We'll continue using Rubeus same as we have with kerberoasting and harvesting since Rubeus has a very simple and easy to understand command to AS-REP roast and attack users with Kerberos pre-authentication disabled. After dumping the hash from Rubeus we'll use hashcat in order to crack the krbasrep5 hash.

>There are other tools out as well for AS-REP Roasting such as kekeo and Impacket's GetNPUsers.py. Rubeus is easier to use because it automatically finds AS-REP Roastable users whereas with GetNPUsers you have to enumerate the users beforehand and know which users may be AS-REP Roastable.

AS-REP Roasting uses **Kerberos 5, etype 23, AS-REP** hash type

**Dumping KRBASREP5 Hashes**

Navigate to the folder where rubeus is located

`Rubeus.exe asreproast`

we use `hashcat` to crack this hashes
