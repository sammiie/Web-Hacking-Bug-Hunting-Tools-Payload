## AMASS

## Installation

Installing Amass is easy by using the precompiled packages, or by using snap on Kali Linux and other popular Linux distros, simply by typing:
`snap install amass`

**Issue I encountered during installion**

snap wasn't installed, so i run `sudo apt install snapd`

then tried `snap install amass` but got error `error: cannot communicate with server: Post "http://localhost/v2/snaps/core": dial unix /run/snapd.socket: connect: no such file or directory`

>Solution
`sudo systemctl restart snapd.service`

`snap version` --output well

Then

![image](https://user-images.githubusercontent.com/16500435/104118513-01459000-532a-11eb-8183-5722b36d2ec6.png)


## USage

A basic subdomain scanning can be performing by running:

`amass -d domain.com`
