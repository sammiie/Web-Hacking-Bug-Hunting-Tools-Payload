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
