#  Processes

The first step I took was to install a FTP daemon(vsftpd) on my Ubuntu system.

<img width="640" height="343" alt="installAndConfiguringFTP" src="https://github.com/user-attachments/assets/f6c587b0-aaa7-43e2-b4d9-d30c537d8aba" />

I then open the vsftpd.conf file, which is the configuration file for the FTP server. I ensured that the server uses a user list, for any FTP users that I might add. I ensured that each server user will be confined to their own directory, instead of having access to other directories in the system as this could have them accessing data they shouldn't be able to. This also ensures that in the event of a breach, an attacker would be confined to one place. Then I defined my passive ports for FTP data transfers. Please observe the comments above the uncommented lines for more details.

<img width="640" height="361" alt="ftpCondfigurations" src="https://github.com/user-attachments/assets/5e78523e-b845-4417-ae83-b3457d518129" />

After making changes to the vsftpd.conf file, I enabled and restarted the vsftpd service so that the configurations are saved. I then used the UFW(Uncomplicated Firewall) to open the FTP port, 20(data channel), 21(control channel), 10000:10100(passive ports, the same as in the vsftpd.conf file).

<img width="640" height="343" alt="3" src="https://github.com/user-attachments/assets/13ec8ec3-c2f5-4f95-a877-ae61ecbeebb8" />

Next, I created a new user, "lesegoftp", and created a password for the user. This will be my FTP user.

<img width="640" height="209" alt="4" src="https://github.com/user-attachments/assets/4d723995-cbfd-44f9-ae30-4706d01105d4" />

I then created a directory a home folder for the user, and an FTP folder. I changed the permission of the FTP folder, ensuring that it had no write permissions, created a folder within it, called the upload folder. This folder would be where all FTP uploads for the user are stored, then ensured the it is owned by lesegoftp. I then added a sample file, with the word "Yessir". After this process, I then added lesegoftp to the vsftpd user list that was defined in the vsftpd.conf file.

<img width="640" height="141" alt="5" src="https://github.com/user-attachments/assets/991be988-ae1c-4d02-9f20-001d9aff584b" />

After creating the FTP server, user, and a file. I switched to my virtual Kali system, here I created a custom bash script(on the left side of the image) that allowed me to carry out a Nmap scan, within a range of IP addresses, scanning for a particular port, in this instance, port 21(FTP port). On the right, you can observe multiple IP addresses were scanned, most had port 21 filtered, but one had it open, that was my target system(Ubuntu).

<img width="640" height="331" alt="6" src="https://github.com/user-attachments/assets/10b7534f-b879-47f4-bb23-2d2c0168805a" />

Afterwards, I began researching and gathering wordlists. Wordlists contain password or usernames from previous breaches. I downloaded some of them, so I could use them for my attack.

<img width="48%" height="330" alt="7" src="https://github.com/user-attachments/assets/9b11026e-fb4c-4487-b4b2-b27f0fd2f209" /> <img width="48%" height="330" alt="8" src="https://github.com/user-attachments/assets/4ef7c8e4-bcee-40e4-961e-e2fab13ee781" />

 then placed the password of the FTP user in one of the wordlists that I would be using.
 
 <img width="640" height="331" alt="9" src="https://github.com/user-attachments/assets/9601f96a-36dd-4725-8bae-9b7748e1595d" />

Here I created a custom Python script. It would used the ftplib library to carry out ftp connections. I created variables that would store the paths of the wordlists that I would be using. The placed all these paths within a list. I then used a loop to iterate over the list, opening each file in the list, reading it's contents and storing it as Words. Then I ran a second loop iterate over each word with the words, strip it of any newline characters. The script then attempts to open a connection to the FTP server, log in with a username(for the server name and username, I operated under the assumption that these details were known.), then the password would be the word that was read from the Words container. It does this until a valid password is found, then prints the password and closes the connection. It will also display any errors.

<img width="423" height="476" alt="10" src="https://github.com/user-attachments/assets/2cd9f0d4-902b-4791-9760-0d911eeb22f3" />

At this point, I executed the python script, as you can observe on the right, there are various incorrect login attempts. But the script keeps running as it was programmed to.

<img width="640" height="360" alt="11" src="https://github.com/user-attachments/assets/90532eb7-2f80-4e59-b625-db335b2be345" />

Back on my Ubuntu system:

I had already fired up Wireshark, to capture packets and observe my network traffic. If you obeserve, you will notice two private IP address, 192.168.8.27(attacker address) and 192.168.8.14(Ubuntu system/victim address), were exchanging data packets.

<img width="640" height="344" alt="12" src="https://github.com/user-attachments/assets/cf86acfa-b1b3-4935-8690-dbe46cda5ea2" />

Back on the Attacker system:

The FTP brute forcer script was still running.

<img width="640" height="360" alt="13" src="https://github.com/user-attachments/assets/1976b776-b56a-4fba-95d4-1f7cbb0f56f0" />

Back on the Target system:

Wireshark continues to capture packets, most of which are incorrect logins.

<img width="640" height="344" alt="14" src="https://github.com/user-attachments/assets/e9d4e04b-fe65-430c-aff9-d12d537d636e" />

Attack system:

At this point, the script has successfully found the password we are looking for. It then stops executing.

<img width="640" height="329" alt="15" src="https://github.com/user-attachments/assets/bea7d199-dbc2-4a5d-a8e9-e0278fa94f9e" />

Target system:

Wireshark reflects this brief log in, and termination of the connection.

<img width="640" height="344" alt="16" src="https://github.com/user-attachments/assets/ebbdecd3-439a-4333-b30a-a661b461f5fc" />

Attack system:

Here,  I then log in to the ftp server of the target, using the credentials that we have acquired. I then use the ''ls -l'' command to retrieve details about the directory we are in, notice that we see the "upload" folder that is in the FTP server. 

<img width="640" height="188" alt="17" src="https://github.com/user-attachments/assets/5655da93-ef4d-4452-a2d7-0876b9e2c327" />

Target System:

Wireshark reflects this second login.

<img width="640" height="344" alt="18" src="https://github.com/user-attachments/assets/2097b032-591d-4a99-9dd5-3df0553f09eb" />

Attacker system:
I then moved into the upload folder, and retrieved the file placed in the folder. Downloading it to my system. 

<img width="640" height="289" alt="19" src="https://github.com/user-attachments/assets/65ce3350-9681-4b24-9f3e-57a38a285a7e" />

Target system: 

Wireshark reflects the file being retrieved, and that the connection was closed.

<img width="640" height="344" alt="20" src="https://github.com/user-attachments/assets/dba01070-d3ba-4180-b657-36b2f8afd9f8" />

Target system:

Now on the target system, I have noticed through Wireshark, that there was a connection that was unauthorised, so I move to observe the logs of FTP server.

<img width="640" height="344" alt="21" src="https://github.com/user-attachments/assets/491523bd-ae7a-44e3-bad5-ce66e2b877c3" />

In the FTP(vsftpd) log file as well as from the Wireshark packet captures, I noticed that there were multiple failed log in attempts from this one IP address, then a successful login. This an indicator of a brute force attack.

<img width="48%" height="344" alt="22" src="https://github.com/user-attachments/assets/d4ec80f8-38c4-4a3c-9e4b-ab1b63dc49ff" /> <img width="48%" height="344" alt="23" src="https://github.com/user-attachments/assets/09c491e1-28fc-422f-afb8-d4aecc640e98" />

The next step to take was to block the attacker IP address. So I used iptables, which allows me to add firewall rules. I set a rule to reject all ftp communication from 192.168.8.27(set) coming into the ftp system.

<img width="640" height="344" alt="24" src="https://github.com/user-attachments/assets/893414f5-d91f-4139-a3f1-db5cf9e49a8f" />

Attacker system:

On the right, I have an Nmap scan for port 21 of the target system, notice that before the firewall rule change, the attacking system could tell that the port was open.

On the left, after the rule change, now the attacking system detects the port as "filtered", this being after the iptable rule were changed to reject packets from  the attacker IP address. 

<img width="48%" height="135" alt="25" src="https://github.com/user-attachments/assets/63906d6d-68eb-45c0-a1f7-b56ba4fed023" /> <img width="48%" height="181" alt="26" src="https://github.com/user-attachments/assets/7bfb03a1-a6ae-4e0d-9783-9c5e0fe5580d" />

Attacker system(on the left):
I attempt to reconnect to the FTP server again, observe that there is no response, as the packets are being rejected.

Target system(on the right):
Wireshark reflects a failed TCP handshake attempt, the target system is rejecting the packets from the attacker. 

<img width="48%" height="59" alt="27" src="https://github.com/user-attachments/assets/a3ba0f73-aea4-4f47-8109-a6fbff4de961" /> <img width="48%" height="344" alt="28" src="https://github.com/user-attachments/assets/35363a45-dc99-4c25-b120-b286c2bf6879" />

On the attacker system:

I observe IP address configurations on the left. The address being 10.0.2.15. 
On the right, the network configurations for my virtual system are set to NAT(Network Address Translation), what this means is that any packet that leaves my virtual system(attacker), the packet info will reflect the address of the host of the virtual system, instead on attacker address. The host in this instance being 192.168.8.27, the address that is blocked on the target system.

<img width="48%" height="31" alt="29" src="https://github.com/user-attachments/assets/673c13b0-2e00-40ba-b329-11adf8aa9c6b" /> <img width="48%" height="316" alt="30" src="https://github.com/user-attachments/assets/a5998411-8184-4e4c-9a3f-33a479dd7b27" />

For the attacker:

I then switch my network adapter, from being a NAT connection, to being a bridged connection. What this will do is ensure that my virtual system will have its own separate IP configuration within my LAN.

<img width="640" height="307" alt="31" src="https://github.com/user-attachments/assets/ad225ded-3213-4ef6-8519-9f888709c613" />

After switching to a bridged connection, I then check the IP configuration of the attacker system, and observe that it has a new IP address, 192.168.8.33.

<img width="640" height="45" alt="32" src="https://github.com/user-attachments/assets/f4387c0b-d4db-4d28-b110-051a68e18a76" />

Attacker(on the left)
Now after switching IP addresses, I am able to gain access to the FTP server, again.

Target(on the right):
Wireshark reflects that the attacker was able to gain access to the server.
This tells me that simply blocking one IP, manually, is not enough of a deterrent to prevent breaches. 

<img width="48%" height="472" alt="33" src="https://github.com/user-attachments/assets/8b1b4aa9-d516-4319-8243-7c4e7ee49c7c" /> <img width="48%" height="344" alt="34" src="https://github.com/user-attachments/assets/e6da09f8-f8d4-463b-aa64-9f424d70041e" />

On the target system:

I then move to change the password of the ftp user.

<img width="640" height="344" alt="35" src="https://github.com/user-attachments/assets/b4391694-ddf0-4cd8-9dc5-ca7c23f7077e" />

Attacker system: 

I reattempted to gain access to the FTP server again, this being after the password was changed. Now I am unable to gain access.

<img width="634" height="229" alt="36" src="https://github.com/user-attachments/assets/9f3a4525-b632-4be9-bfe2-f074e68f65f1" />

On the target system:

Another thing that I noticed while observing the packet captures, was that the packets were transmitted in plaintext. This being because FTP is unsecured. This is also a problem because if someone else was sniffing on the network, they could recover user passwords and usernames.

<img width="640" height="344" alt="37" src="https://github.com/user-attachments/assets/b351a18b-7d1f-44fd-91d7-996299e77a79" />

After making this observation, I then did research and learned to  encrypt the FTP connection. So here, I generated a self-signed SSL certificate.

<img width="504" height="512" alt="38" src="https://github.com/user-attachments/assets/f8f9a0fd-3c56-4362-a94d-efe5c0534e98" />

After generating the certificate and key, I then went back into the vsftpd.conf file to enable ssl connections, ensuring that all data connections and logins will be encrypted. Then added paths to the ssl certificate i generated as well as its key.

<img width="640" height="459" alt="39" src="https://github.com/user-attachments/assets/7d1341b6-7000-4009-8dda-8de784683d7e" />

On the attacker system:

I then changed the Python script slightly. I moved the wordlists and the list containing them to be globally recognised(meaning they weren't tied to one function), making them accessible to the new function that I added. The new function is an FTPS brute forcer function, using the same logic as the FTP brute force, but for FTPS connection. I kept the FTP option available, because It might be reusable in the future. 

<img width="290" height="466" alt="40" src="https://github.com/user-attachments/assets/5c6ceba7-8259-46af-9d02-e728cfcaa3df" />

On Target system:

I observed the connection after retrieving the password from the improved script.

On the Attacker(the right):
I used a third party ftp client application to connect to the FTPS server and retrieve the file I wanted.

<img width="48%" height="321" alt="41" src="https://github.com/user-attachments/assets/41844046-386f-4022-8ae8-80b766fe4fed" /> <img width="48%" height="307" alt="42" src="https://github.com/user-attachments/assets/b35c32b1-e8cd-465c-b463-067dcfc0ef03" />

On the target system:
The difference in the packet capture is visible, on the right, the packets were encrypted, and their contents secure, even over transmission.

On the left, the packets were unsecured, allowing anyone to see their contents, especially if they were monitoring network traffic.

<img width="48%" height="321" alt="43" src="https://github.com/user-attachments/assets/41189096-65b3-481c-8d34-c87a3e31d3f4" /> <img width="48%" height="344" alt="44" src="https://github.com/user-attachments/assets/f6be7f7d-ddbb-45f0-b780-9ca8273571fd" />

After this, I realised that blocking IP address manually would be tedious and inefficient, so I began doing research on how to automate the blocking of process of any IPs that may try to login multiple times(a brute force signature). I learned about a tool, Fail2Ban, that could help do this.
I installed Fail2Ban on my target system.

<img width="640" height="107" alt="45" src="https://github.com/user-attachments/assets/0dda677a-c976-4072-b8bb-1d39a58b9bf5" />

On the left, I created a "jail" file. So Fail2Ban uses these jail files and their rules to observe traffic for a particular protocol, in this case ftp and ftps. I then programmed it to read the log files of the vsftpd daemon. The log files(/var/log/vsftpd.log) will indicate any login attempts by an IP address. Now the jail rules are that each IP address can only attempt to access the server 3 times, attempting to do more than this will result in the IP facing a ban. This process is rate-limiting, allowing me to counter bruteforce/online password attacks. In this case, I set the ban for 30 minutes(1800 seconds).  Fail2Ban will also check the logs for any attempts that were made to breach the system more than 3 times within the last 5 minutes(findtime = 300), and ban them. 

On the right, I restarted the program and checked how its format would look. It would indicate all banned IPs. 

<img width="640" height="321" alt="46" src="https://github.com/user-attachments/assets/0fbdf6c2-73ad-43c4-9559-26e5ae6ab1f5" />

On the Attack system:

I then attempted to run the ftp brute force script again(the bottom portion of the image), after implementing Fail2Ban on the target, the script stop receiving a response.

<img width="640" height="402" alt="47" src="https://github.com/user-attachments/assets/88e0bc5a-b8a9-4b25-8e0b-d120a112181f" />

On the target system:

On the left I observe that attacker IP is blocked, this happened after its third attempt to login.

On the right, I observed the packets on Wireshark, from the connection attempted and eventually the attacker IP being blocked, at the bottom.

<img width="48%" height="321" alt="48" src="https://github.com/user-attachments/assets/4aab7001-3c13-4cb9-8040-77bcd821461d" /> <img width="48%" height="321" alt="49" src="https://github.com/user-attachments/assets/18166ac3-20ff-44f8-8328-8e58464510aa" />

