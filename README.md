# Vulnerability Scanning and Exploit Lab
Metasploitable Vulnerability Lab:

In this lab I will be Utilizing Nessus, Metasploit, Metasploitable2, and Burp Suite to perform vulnerability scanning on a virtual machine and a web application (one that was intentionally setup for scanning purposes).

Note: I will be utilizing kali linux in this lab with all the necessary tools already installed and configured.

First, I will start metasploitable2 in VirtualBox configured with Host-only adapter as you should never expose metasploitable2 to an open network. The credentials are user: msfadmin pass: msfadmin. Once the machine is running, the only thing I will do in this machine is obtain the ip address of the box.

![metasploitable1](https://github.com/user-attachments/assets/854c6de6-bc25-470d-ba47-d53732e2a65f)

Next, I am going to open the Nessus dashboard and create a new scan and fill in the corresponding information. This first scan is just a host discovery, which could alternatively be done in nmap.

![metasploitable2](https://github.com/user-attachments/assets/43603334-1666-45a4-9186-d5179c681cfd)

![metasploitable3](https://github.com/user-attachments/assets/6854293f-e28d-4848-9be2-60d741092dab)

![metasploitable4](https://github.com/user-attachments/assets/75336e55-3070-47f0-b105-00a1b1708f59)

Next, I am going to run a basic network scan to find vulnerabilities. 

![metasploitable5](https://github.com/user-attachments/assets/c5e32de2-a2f6-4fc3-bd35-5e61d0af04be)

While the Nessus scan is running I am going to run a nmap scan as well with "nmap -sV -p- -v -n 192.168.56.109". "-sV" indicates port and version scanning, "-p-" scans on all ports, "-v" is verbose output, and "-n" is never do dns resolution.

![nmap1](https://github.com/user-attachments/assets/e52104be-fd90-4376-ab99-0334264aa692)

![nmap2](https://github.com/user-attachments/assets/6fff5fa4-896f-4439-81cf-3e8598b0ead5)

The results from nmap can be used in conjunction with metasploit and exploitdb to find vulnerabilities in the services running on the vm based on the service version and open ports; However, now that the Nessus network scan is complete I can see a total of 67 listed vulnerabilities and explore them and read their descriptions.

![VulnScan](https://github.com/user-attachments/assets/98f98cfa-f637-445b-a207-06df0523fb05)

![VulnScan2](https://github.com/user-attachments/assets/a28166bb-9173-40ac-937b-058bc089b5cf)

Nessus categorizes vulnerabilities via their severity, common vulnerability scoring system, exploit prediction scoring system, name, family, and total count of similar issues. Just from this overview page alone I can immediatelty identify 6 potential ways to gain access to the machine.

The NFS shares on metasploitable are open and accessible to anyone and everyone. Meaning anyone can locally mount their own share and push data to the machine or scan and mount shares from the system

![RPCinfo](https://github.com/user-attachments/assets/b73c0fbc-2c2d-4d01-a539-9123d2eb2394)

![opennfs](https://github.com/user-attachments/assets/00dde9cb-70d0-4b39-a476-aa8c86b69ad2)

![opennfs2](https://github.com/user-attachments/assets/ecbd09c7-2409-4aa2-929f-8a251756b4de)

Next on the list, VNC (Virtual Network Computing) has a weak password which nessus was able to identify. Meaning anyone can remotely access the system by just using the password "password" and obtain root access.

![vncviewer](https://github.com/user-attachments/assets/8f0544d9-2345-49e4-a9bf-64c48dd25d1b)

Next is an open listening port. Nessus identified that port 1524 is open and listening without requiring any authentication meaning, again, anyone can connect and access the system.

![OpenListen](https://github.com/user-attachments/assets/de68b39e-dd70-4ca2-9159-2895b05fc192)

![OpenListen2](https://github.com/user-attachments/assets/92cef5f9-532d-4ed1-966e-144b498b1439)

Next is issues with apache tomcat running on system. Nessus was able to identify the service version of tomcat and detect that version 5.5 has reached it's Security End of Life meaning it will no longer recieve security updates so the version should be updated to one that is currently supported. Additionally, a vulnerability was found in the AJP connecter allowing request injection. Attackers could exploit this to read application files from a vulnerable web server, which can be done through metasploit.

![ghostcat](https://github.com/user-attachments/assets/b6458c01-c0e3-41b3-85bd-b27c06b27f20)

Next is a vulnerability in postGreSQL. This can be exploiting in metasploit by searching for postgresql exploits, loading the exploit, configuring it, and running the exploit.

![postgresql](https://github.com/user-attachments/assets/f5f67c38-605e-4a6d-8058-c8631e9308b1)

This obtains a reverse tcp shell and the RSA private key is easily readable.

![postgresql2](https://github.com/user-attachments/assets/57dc8de9-e8bf-465e-9321-9431beebec35)

Next up is the Samba Badlock vulnerability, which exploits a flaw in the SMB server to open a reverse shell while using an anonymous login to the SMB server. First done in metasploit

![Badlock1](https://github.com/user-attachments/assets/61d7a8d3-1a66-47bb-8eaf-4c5aebfaceff)

![Badlock2](https://github.com/user-attachments/assets/9bc5e936-a3aa-4fbc-ad92-488487aca689)

And done manually by connecting to the smb sever with an anonymous login and running a reverse connection back to a netcat listener on the local system.

![Badlock3](https://github.com/user-attachments/assets/0c2d949e-8c5b-40ed-a98a-8e90246619d6)

# Manual Web Application Vulnerability Detection With Burp Suite

A bit of a different exercise, I will be following along with the portswigger HTTP interception lab.

First, is to open burp suite and open the browser in the proxy tab. Then navigate to the portswigger provider website. Once on the page, the burp proxy intercept can be turned on and the website can be explored to begin intercepting traffic. 

![Burp1](https://github.com/user-attachments/assets/d6e4557e-5429-49f5-9a78-1df0f71dca68)

![Burp2](https://github.com/user-attachments/assets/523b34a4-d3f5-46eb-a9a7-7de45b3baa2c)

By adding an item to the cart and intercepting the traffic, the HTTP POST is viewable and more importantly modifiable. In this case the price parameter can be changed and then forwarded to trick the website to load the item as only costing $0.01. In a similar vein, vulnerable web applications can have the POST method modified to include commands which allows for remote code execution on the server, but this lab doesn't cover that however HackTheBox has several modules on this topic which I have completed and I reccomend others to check out.

![Burp3](https://github.com/user-attachments/assets/cc7f6e2f-8b52-4cdc-9f90-db906b778632)

![Burp4](https://github.com/user-attachments/assets/ff853ee5-c024-473f-85fb-77e07960d4af)

The next exercise is similar, but utilizes burp's repeater function.

![Burp5](https://github.com/user-attachments/assets/1fa68c27-e4ea-4611-bd96-51e9a71a6350)

After visiting the site again in burp's browser and exploring, the previously sent traffic is accessible in the http history and from there can be sent to the repeater. The repeater is useful for experimenting and quickly testing modifications to the HTTP method.

![Burp6](https://github.com/user-attachments/assets/555764a0-6d5f-492e-89c6-a07f74da57b0)

In this case the productId parameter is setup to handle integer input, but isn't setup correctly to handle unexpected input such as a string value which can be discovered by testing for input based vulnerabilities. In the event of recieveing the a string the server sends back a verbose error response that contains a stack trace and reveals that the website is using Apache Struts and the version, which can be useful information for an attacker to obtain.

![Burp7](https://github.com/user-attachments/assets/3df60eb5-b1f1-423c-9bcf-eb75fa0d76c7)

![Burp8](https://github.com/user-attachments/assets/31397774-a911-499f-a78f-f096d48e6a8d)

# Home Network Scanning Exercise

Lastly, I will perform a quick scan against my home network using Nessus. Nessus inidicated a Medium severity vulnerability found, mDNS Detection. This vulnerability would allow anyone to uncover information such as operating system type and exact version, hostname, and a list of services running. To remedy this I implemented a firewall rule to filter incoming traffic to UDP port 5353.

![firewall1](https://github.com/user-attachments/assets/4c28591c-fdac-48c5-9047-bd496dc0b3cd)

![firewall2](https://github.com/user-attachments/assets/339762f5-475a-4bf4-bab3-f4743ae79571)

![firewall3](https://github.com/user-attachments/assets/dcec470e-0661-4bda-9974-1bc6d3a7f9db)

![firewall4](https://github.com/user-attachments/assets/cd364c55-7064-40bc-9df8-bd4a16442b39)

That's all for this project, thanks for reading!
