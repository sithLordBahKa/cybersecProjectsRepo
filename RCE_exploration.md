# Objective of this project
-Understand how SambaCry worked, and the threat it posed.

-Learn about the harmful nature of RCEs

-Attempt to exfiltrate data on the target system

# Processes

The first step I took for this project was to find an older version of the Samba application, and install it on my victim system, which uses the Xubuntu OS. Samba is an application on Linux systems that allows file and printer sharing across various OSs and devices. It works via the SMB(Server Message Block) protocol.

<img width="1280" height="773" alt="1_SambaCry" src="https://github.com/user-attachments/assets/a2cb7cca-600a-4534-b814-b84f392ff9ac" />

Then I proceeded to set up the Samba configuration file and share folder.

<img width="48%" height="633" alt="4SettinUpTheConfigFile" src="https://github.com/user-attachments/assets/178181b8-3b17-413e-bc85-fa698e92cdfd" /> <img width="48%" height="453" alt="settingUpShareFolder" src="https://github.com/user-attachments/assets/6e1d30d4-ef9e-4cb1-b541-11123f27c3f3" />

From there I went on to check the target system's IP address, 192.168.8.50, and note it.

<img width="1280" height="770" alt="5XubuntuTargetIp" src="https://github.com/user-attachments/assets/666a714d-f83e-4e4f-b59b-76e283c3683d" />

Now that my victim system was all set, it was time to move as an attacker.

I used Nmap to perform an aggressive SYN scan, that would reveal detailed information of any of the protocols and services running on the target, and their respective ports. As you can see from the screenshot,I discovered 2 open ports, 139 and 445. Port 139 uses NETBIOS to run SMB protocols, while port 445 runs SMB directly, without the need of NETBIOS.
I had attempted to perform OS fingerprinting through Nmap(forgot to take a screenshot and add it), however that failed to ascertain the OS of the target. However, one can infer that since the ports were operating a Samba application("Samba smbd 3.X - 4.X"), then the target OS, is probably a Linux OS.

<img width="613" height="432" alt="6TargetScan" src="https://github.com/user-attachments/assets/d2b9cbfe-d27c-432b-ac31-701cece785c3" />

So since I learned that the target was running a Samba application, and which were the most likely versions. I need to gather information about any known vulnerabilities that would work on this version. I used [www.cve.org] to perform my search.

<img width="1366" height="705" alt="7Recon" src="https://github.com/user-attachments/assets/dedd7ea2-3283-472a-a7bc-f1d329b62b5a" />

I found a vulnerability(cve-2017-7494) that would likely match the application versions running on my target system. I then proceeded to fire up Metasploit, and search if the vulnerability was listed on its list of vulnerabilities, it was.

<img width="48%" height="705" alt="8FiringUpMSF" src="https://github.com/user-attachments/assets/ae4f262d-cb4c-496b-bc6a-74cdd14d59b6" />  <img width="48%" height="705" alt="9searchIFTheExploitIsAvailableONMSF" src="https://github.com/user-attachments/assets/f0e68ea5-5b23-4a46-bc7a-eff5eb316165" />

Then I pulled up more information about the information from






