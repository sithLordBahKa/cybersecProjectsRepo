# Objective of this project
-Understand how SambaCry worked, and the threat it posed.

-Learn about the harmful nature of RCEs.

-Attempt to exfiltrate data on the target system.

-Demonstrate skills such as target enumeration, vulnerability exploitation, scripting capabilities, ability to use various tools and resources to achieve my objective.

-Perform password/hash cracking functions.

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

At this point, I had selected to use the exploit, and I wanted to observe what payloads where available for use.

<img width="677" height="468" alt="12PotentialPayload" src="https://github.com/user-attachments/assets/5ad4e908-613a-4c89-98d7-51a825210c87" />

I observed that the only available payload targeted Unix systems, so this meant I had to change the target option to a Linux option,

<img width="533" height="429" alt="showAndSetTarget" src="https://github.com/user-attachments/assets/9f20cef3-52c2-4527-b810-d89d143d563f" />

After changing the target, I needed to see what payloads were available. A new set of payloads were now displayed and the one that I wanted to use was  "payload/linux/x64/shell_reverse_tcp", which would create a listener on my attacker system, and upon successful connection to the the target, it would grant me command shell.

<img width="1150" height="479" alt="13SettingAPayload" src="https://github.com/user-attachments/assets/73da9023-fda5-45c5-97ec-49894431b127" />

I set the payload, then set the target IP address and executed the exploit. The exploit uploaded a share object to the target's samba share folder, then created a command shell session, this is how the attacker will now interact with the victim's system.

<img width="1133" height="264" alt="14exploitLaunched" src="https://github.com/user-attachments/assets/e111ec6c-19e7-4396-a78a-aa2a1cedf4d1" />

So now that the connection was established and I was within the target's system, I needed to see what I could do, and how I could move. I was able to determine that the exploit instantly granted me root access to the target's system, this allowed me to find out more about what was within the system, as well as navigate the system.

<img width="631" height="499" alt="15gainingAccess" src="https://github.com/user-attachments/assets/0a0baba5-0fe8-4fa1-a136-2c4be56edf35" />

As an attacker, some of the most important information I could want, was password information, seeing that this was a Linux system, I moved to check what was within the shadow file. This is the file that typically houses password information for a Linux system.

<img width="48%" height="526" alt="16ReadingTheShadowfile" src="https://github.com/user-attachments/assets/7c7ab444-2ffe-42a3-80ab-d2dbc0bd6d59" />  <img width="48%" height="480" alt="17ShadowfileContinued" src="https://github.com/user-attachments/assets/b964bc96-37f5-465a-b406-74245c114982" />

The next order of business was to search for more data, some other data that would be valuable would be financial and customer data, important documents, anything that may hold intellectual property or business dealings. In my case, I just searched for anything that could be a spreadsheet, I found 2, "customers.xlsx" and "BTC_Codification_30October2023.xls". I also searched for more data, specifically, a file containing hash values(testingHashes.txt), much like the shadow file, I will explain the purpose of this later on.

<img width="48%" height="144" alt="18SearchingForSomeData" src="https://github.com/user-attachments/assets/88395537-4238-404f-a5d6-ad261d9f0b3d" /> <img width="48%" height="75" alt="19SearchContinues" src="https://github.com/user-attachments/assets/0e510cdc-50d2-43fa-bf54-b6a5e422ce90" />


So now that I knew what I was looking for, and the locations to find the data, I needed to gather everything I wanted. So I archived all my desired data into the Data.tar file, this included the shadow file, the 2 spreadsheets, and the other hash value file.

<img width="1356" height="196" alt="20ArchivingTargetData" src="https://github.com/user-attachments/assets/cb6d892e-5d82-4517-9653-f50cb95305b2" />

After archiving the data, I opened a second command shell on the target system, here I created a listener via port 8080. This would wait to receive any incoming data, and direct it to the /tmp/targetData.data file.  

<img width="562" height="83" alt="21StartATCPListener" src="https://github.com/user-attachments/assets/d3828d8f-fb53-4160-bd23-c5989673ac0e" />

Now going back to the command shell session established by the exploit. Here I selected the Data.tar file, used base64 encoding on it, converted it to EBCDIC(Extended Binary Coded Decimal Interchange Code) format, then exfiltrated the data to my attacker system. I inputted the attacker IP address, and the port that was listening on the attacker system.

<img width="528" height="68" alt="22PerformingDataExfil" src="https://github.com/user-attachments/assets/bb901d45-c9ef-452a-9f87-2db794c7e287" />

After exfiltrating the data, I needed to clean up and erase my activity, so I proceeded to deleted the files that I created while gathering and archiving the target's data. I deleted the Data.tar file and the targetData directory. The last thing I did was shred the logs on the target's system, this helps hide my presence and activity. The I exited the Command shell session.

<img width="429" height="225" alt="cleanUpAndExit" src="https://github.com/user-attachments/assets/f8a82418-ccfe-463c-8772-2b444e57a38d" />


Then back to the command shell that the listener was active on, here I can observe that the target and my attacker system connected.

<img width="575" height="111" alt="23AfterPerformingDataexfil" src="https://github.com/user-attachments/assets/0b217ce4-ddd4-4814-8596-4884252e0e37" />

From there, I navigated to the /tmp directory, performed a listing to observe if the targetData.data file was there, then I proceeded to convert any data within the file to an ASCII format, used base64 to decode the data, and redirected the output to a targetData.tar file. 

<img width="48%" height="418" alt="24NavigatingtoTMP" src="https://github.com/user-attachments/assets/b6c727a6-cb88-4c58-b294-d990441ec4a5" /> <img width="48&" height="277" alt="25ObservingCreationOfTheTarFile" src="https://github.com/user-attachments/assets/1f74c9d2-e436-47f4-9185-088e3f7798f5" />

After creating the targetData.tar file, I then proceeded to extracted all the data that was archived within it, using the tar xvf command. We can observe that not only were the files extracted, but directories were created, within the /tmp directory. The 2 directories being the "etc" and "home" directories. 

<img width="655" height="376" alt="26DataExtracted" src="https://github.com/user-attachments/assets/2581d90f-d9d3-4a36-86b4-16fdd65ee197" />


I then navigated to one of these new directories, the /tmp/home directory. Here I can observe that the were folders within the directory, specifically the Documents folder, which houses the testingHashes.txt file, as well as the spreadsheet data from the target.

<img width="488" height="385" alt="27SeeingTheLoot" src="https://github.com/user-attachments/assets/e7fb7f52-3b78-48d7-b531-d86584a9674f" />

Now getting into the testingHashses.txt file. The file was setup to emulate the shadow file. The objective of retrieving this file was so that I can demonstrate a type of password attacker. Opening the file, we can observe that it essentially has hash values for passwords that different users use within a system.

<img width="703" height="326" alt="28observeTargetHashes" src="https://github.com/user-attachments/assets/009b4bb2-a242-46e8-96ce-2d5e62880fa0" />


Back in my FTP project, I had demonstrated what is essentially an online password attack, which required bruteforcing passwords by entering them on what would be a log-in form, or in my case bruteforcing a log-in session. Now in this instance, I have gained access to a system, and I was able to retrieve a file that houses password hashes for different users, I don't need need to try an online attack password if I want access to the system.

Online password attacks tend to take up more resources, and as observed in the FTP project, they can be mitigated by using rate-limiting and account lockouts.

Now that I have retrieved the hash file from the victim system, it allows me to view usernames, and their hash values. More importantly, I can now perform offline password attacks, I don't have to worry about my IP address being blocked because I triggered a security application on the target. 

So, I copied the hashes in the retrieved testingHashes.txt file, into another file, and then I proceeded to use John the Ripper, a password cracking tool, to try and crack the hashes within the file. I supplied John with a wordlist and the file that contained the copied hashes.

<img width="48%" height="267" alt="copyHashes" src="https://github.com/user-attachments/assets/3a091078-eae5-4718-8607-16d6cde63f35" /> <img width="48%" height="325" alt="tryingJackTR" src="https://github.com/user-attachments/assets/9d1d6069-beb6-45d1-b061-d2b03b735831" />

While John was running and trying various hashing algorithms to crack the hashes, I created a hash cracker script. I used some simple hashing algorithms, purely for demonstration. The program would observe a hash's length and based on that, it would try to use a hashing algorithm that typically outputs hashes of that length. If a hash of an unknown length was detected, it would simply print that an unknown hash was detected. Then I supplied it with a small wordlist to try and hash each word, and if the hash value of the word in the wordlist matched the detected hash value from the target's hash file, then that word would be the password that was used for the user in the hash file.

<img width="842" height="469" alt="29simpleHashCracker" src="https://github.com/user-attachments/assets/a58abec5-e760-4af7-8219-8e49b9481123" />

Then, I executed the script, and supplied it with the file that contained the hashes(testingHashes.txt). The script was able to detect two unknown hashes, and it failed to find a match for one of the hashes. This would most likely be because the word was not in the wordlist that I supplied, so even if the hash length was a match for one of the algorithms, without a word that outputs the same hash,noting can be done. However, other hash values had a match, the script outputted the words that were a match for certain hashes, the hash value that was compared, and the hashing algorithm.

<img width="691" height="405" alt="30RunningTheHashCracker" src="https://github.com/user-attachments/assets/bc71c985-2eb8-4f23-93c3-81da32c67b65" />
