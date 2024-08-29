# Vulnerability Scanning and Exploit Lab
Metasploitable Vulnerability Lab:

In this lab I will be Utilizing Nessus, Metasploit, Metasploitable2, and Burp Suite to perform vulnerability scanning on a virtual machine and a web application (one that was intentionally setup for scanning purposes).

Note: I will be utilizing kali linux in this lab with all the necessary tools already installed and configured.

First, I will start metasploitable2 in VirtualBox configured with Host-only adapter as you should never expose metasploitable2 to an open network. The credentials are user: msfadmin pass: msfadmin. Once the machine is running, the only thing I will do in this machine is obtain the ip address of the box.

[[/MetasploitableScreenshots/metasploitable2.jpg]]

Next, I am going to open the Nessus dashboard and create a new scan and fill in the corresponding information. This first scan is just a host discovery, which could alternatively be done in nmap.

[[/MetasploitableScreenshots/metasploitable2.jpg]]

[[/MetasploitableScreenshots/metasploitable3.jpg]]

[[/MetasploitableScreenshots/metasploitable4.jpg]]

Next, I am going to run a basic network scan to find vulnerabilities. 

[[/MetasploitableScreenshots/metasploitable5.jpg]]

While the Nessus scan is running I am going to run a nmap scan as well with "nmap -sV -p- -v -n 192.168.56.109". "-sV" indicates port and version scanning, "-p-" scans on all ports, "-v" is verbose output, and "-n" is never do dns resolution.

[[/MetasploitableScreenshots/nmap1.jpg]]

[[/MetasploitableScreenshots/nmap2.jpg]]

The results from nmap can be used in conjunction with metasploit and exploitdb to find vulnerabilities in services running on the vm; However, now that the Nessus network scan is complete I can see a total of 67 listed vulnerabilities and explore them and read their descriptions.

[[/MetasploitableScreenshots/vulnscan.jpg]]

[[/MetasploitableScreenshots/vulnscan2.jpg]]

Nessus categorizes vulnerabilities via their severity, common vulnerability scoring system, exploit prediction scoring system, name, family, and total count of similar issues. Just from this overview page alone I can immediatelty identify 7 potential ways to gain access to the machine.

