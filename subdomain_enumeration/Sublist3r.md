## Installation

Clone repo from git

![image](https://user-images.githubusercontent.com/16500435/104125605-29e47e80-5358-11eb-9864-9c9e258a81ea.png)

Install dependencies, this can be done by navigating to the Sublist3r directory and execute the command;

`sudo pip install -r requirements.txt`

![image](https://user-images.githubusercontent.com/16500435/104125673-89db2500-5358-11eb-8a15-6fa1368bab8a.png)


## Usage

Navigate to the subbrute directory

`python3 sublist3r.py -h`

![image](https://user-images.githubusercontent.com/16500435/104125716-dde60980-5358-11eb-9b60-cede32dfaf48.png)


**basic subdomain enumeration**

`python3 sublist3r.py -d example.com`

![image](https://user-images.githubusercontent.com/16500435/104125773-41703700-5359-11eb-9898-f110012870e8.png)

This subdomain scanner also includes a cool feature that only scans subdomains that have certain ports open. For example:

`python sublist3r.py -d wikipedia.com.com -p 80,443`

This request will perform a subdomain enumeration and filter only those hosts with 80 and 443 ports open.
