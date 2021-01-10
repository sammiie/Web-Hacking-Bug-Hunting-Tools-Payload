## AMASS

**Guide** [https://github.com/sammiie/Amass/blob/master/doc/user_guide.md](url)

## Installation

**Installation Guide** [https://github.com/sammiie/Amass/blob/master/doc/install.md](url)

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

![image](https://user-images.githubusercontent.com/16500435/104118628-d0b22600-532a-11eb-81a7-ed5a21b9447a.png)

**Guide** [https://github.com/sammiie/Amass/blob/master/doc/user_guide.md](url)


**A basic subdomain scanning can be performing by running:**

`amass enum -d domain.com`

![image](https://user-images.githubusercontent.com/16500435/104119186-1e7c5d80-532e-11eb-8ebe-008c96985acd.png)
