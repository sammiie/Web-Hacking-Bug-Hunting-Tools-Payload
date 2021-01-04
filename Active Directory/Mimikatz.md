## Pass the ticket Attack

Navigate to the directory where you have mimikatz

then run the command

`mimikatz.exe`

in mimikatz run `privilege::debug` and make sure the output is `[output '20' OK]` if it does not that means you do not have the administrator privileges to properly run mimikatz

Now run `sekurlsa::tickets /export`

this will export all of the .kirbi tickets into the directory that you are currently in

![image](https://user-images.githubusercontent.com/16500435/103574689-aac2f680-4ed0-11eb-8e9e-b0fae7415674.png)

>When looking for which ticket to impersonate I would recommend looking for an administrator ticket from the krbtgt just like the one outlined in red below.

![image](https://user-images.githubusercontent.com/16500435/103575037-4b191b00-4ed1-11eb-8ffc-d4d120080805.png)

**Pass the ticket using mimikatz**

Now that we have our ticket ready we can now perform a pass the ticket attack to gain domain admin privileges.

run the command `kerberos::ptt <ticket>` inside of mimikatz with the ticket that you harvested from earlier. It will cache and impersonate the given ticket

![image](https://user-images.githubusercontent.com/16500435/103575384-edd19980-4ed1-11eb-863a-142fa66972e2.png)

we can then use `klist` to verify that we successfully impersonated the ticket by listing our cached tickets. (We will not be using mimikatz for the rest of the attack.)

![image](https://user-images.githubusercontent.com/16500435/103575883-9d0e7080-4ed2-11eb-890a-7ba409abb995.png)

we can verify that we have impersonated the ticket giving us the same rights as the TGT we're impersonating by looking at the admin share as shown below.

![image](https://user-images.githubusercontent.com/16500435/103576465-7a308c00-4ed3-11eb-9022-3e84d25c14f9.png)



## Golden/Silver Ticket Attacks

A silver ticket can sometimes be better used in engagements rather than a golden ticket because it is a little more discreet. If stealth and staying undetected matter then a silver ticket is probably a better option than a golden ticket however the approach to creating one is the exact same. The key difference between the two tickets is that a silver ticket is limited to the service that is targeted whereas a golden ticket has access to any Kerberos service.

A specific use scenario for a silver ticket would be that you want to access the domain's SQL server however your current compromised user does not have access to that server. You can find an accessible service account to get a foothold with by kerberoasting that service, you can then dump the service hash and then impersonate their TGT in order to request a service ticket for the SQL service from the KDC allowing you access to the domain's SQL server.

**KRBTGT Overview** 

In order to fully understand how these attacks work you need to understand what the difference between a KRBTGT and a TGT is. A KRBTGT is the service account for the KDC this is the Key Distribution Center that issues all of the tickets to the clients. If you impersonate this account and create a golden ticket form the KRBTGT you give yourself the ability to create a service ticket for anything you want. A TGT is a ticket to a service account issued by the KDC and can only access that service the TGT is from like the SQLService ticket.

**Golden/Silver Ticket Attack Overview**

A golden ticket attack works by dumping the ticket-granting ticket of any user on the domain this would preferably be a domain admin however for a golden ticket you would dump the krbtgt ticket and for a silver ticket, you would dump any service or domain admin ticket. This will provide you with the service/domain admin account's SID or security identifier that is a unique identifier for each user account, as well as the NTLM hash. You then use these details inside of a mimikatz golden ticket attack in order to create a TGT that impersonates the given service account information.

**Dump the krbtgt hash**

Navigate to mimikatz diretcory

run `mimikatz.exe`

then `privilege::debug` - ensure this outputs [privilege '20' ok]

now run `lsadump::lsa /inject /name:krbtgt`

>This will dump the hash as well as the security identifier needed to create a Golden Ticket. To create a silver ticket you need to change the /name: to dump the hash of either a domain admin account or a service account such as the SQLService account.

![image](https://user-images.githubusercontent.com/16500435/103580290-3b520480-4eda-11eb-9bb6-3763b50b859a.png)


**Dump User/service silver ticket hash**
`lsadump::lsa /inject /name:Administrator` (user=Administrator)

![image](https://user-images.githubusercontent.com/16500435/103580389-663c5880-4eda-11eb-8022-b329cb943c03.png)

`lsadump::lsa /inject /name:SQLService`  (service = SQLService)

![image](https://user-images.githubusercontent.com/16500435/103580481-8ff57f80-4eda-11eb-80a1-87ad6da39026.png)

After creating the golden/silver ticket we can use the Golden/Silver Ticket to access other machines as shown below;

`misc::cmd` -  this will open a new elevated command prompt with the given ticket in mimikatz.

![image](https://user-images.githubusercontent.com/16500435/103580798-1a3de380-4edb-11eb-90bb-ed7709deccaf.png)

