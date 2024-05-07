<h1>Brute-Force Attack Log Analysis on CLI Lab</h1>


<h2>Description</h2>
In this lab, I was given two log files: auth.log and wtmp logs, and there was a suspected attack. My job was to investigate and determine if there was a breach. I began by examining auth.log with the cat command, which outputs the contents of the entire file. There was a log where a user named "cyberjunkie" was created and instantly given sudo or elevated privileges, indicating a potential successful attack. Next, I filtered the logs using grep and cut to look for any unusual spikes in failed login attempts, signaling a brute force or spraying attack. I found multiple login attempts against different accounts such as admin, backup, and root. Once the attack was confirmed, I investigated the attacking IP using open-source intelligence, found the location of the IP, and discovered it was a hosted Amazon server. I then went back to the logs and filtered for keywords signifying a successful login attempt by the attacker. Filtering for the word "accepted" revealed that the malicious IP logged into the user "root" twice and the user "cyberjunkie" once. Using the grep command to search for "root" showed when the attacker successfully gained access to the account and for how long the SSH session was. The first session lasted less than a second, and the second session lasted close to five minutes. Moreover, during that time frame of the session, I found in the logs that it was when the user "cyberjunkie" was created and given root privileges. Once the attacker closed the session as root, they SSH'd back in with the newly created account, ran the command "sudo cat /etc/shadow" revealing encrypted passwords, and used the curl command to download a .sh (shell) file from GitHub. Lastly, I researched the MITRE ATT&CK page to find the attack vectors the attacker used and how to harden the security posture to ensure the breach does not occur again.
<br />


<h2>Utilities Used</h2>

- <b>Linux Terminal</b> 
- <b>Oracle Virtual Box</b>
- <b>VirusTotal</b>
- <b>ipinfo</b>


<h2>Environments Used </h2>
- <b>Kali Linux VM </b> 

<h2>Lab Overview:</h2>

<p align="center">
The log files to examine: auth.log posses authentication logs, both failed and sucecessful while wtmp logs also shows successful session logins with timestamp and IP address.<br/>
<img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/71385460-bc58-4534-8768-3fd62f727794)"  alt="SSH Brute-Force Analysis"/>
<br />
<br />
Using the cat command on auth.log to output the entire log file, I immediately noticed suspicous activcity with a user "cyberjunkie" being created and given sudo or root priviliges, likely confirming a sucessful attack occured. However, now it is time to find out how it began and if there are other indicators of compromise or attack.<br/>
<img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/d8aaddcd-0ddc-4e01-bb1a-5432854de865"  alt="SSH Brute-Force Analysis"/>
<br />
<br />
I began using CLI-fu (different command line tools) to try and uncover more information about the attack, starting by seeing if there were any suspicious failed login logs indicating a potentional password spraying or brute force attack. And it appeared that there was a brute force attack. The attacker first targeted an account "admin" however, the user did not exist and then began targeting the accounts "root" and "backup" which were accounts. <br/>
<img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/c9672fda-4882-4c89-ade5-26e98a8ac7fd"  alt="SSH Brute-Force Analysis"/>
<br />
<br />
I first sorted for the logs with the string "Failed Password" while excluding invalid to only show the logs that were generated from failed logins to active accounts using the grep command with the -i for case insesnsitivity and -v to exclude. I then added a cut command to the previous command to only pull out the the date, time, account, and IP of the attack as well as sorting and counting the attempts for easier viewing. The output revealed that the backup account had 9 and the root account had 6 failed login attempts. <br/>
<img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/8e42d0a6-9309-4930-a1ed-eb45965a4b83"  alt="SSH Brute-Force Analysis"/>
<img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/e139ab5d-9747-4b6b-92f3-21878916a0e2"  alt="SSH Brute-Force Analysis"/>
<br />
<br />
It is now clear that there was a single IP that conducted the brute-force attack against multiple accounts. I began to investigate the IP to see if there is any information I can find out about it. On the command line I used the curl command to access the website ipinfo[.]io with the IP that conducted the attack and found that it is an amazon webserver from Mumbai, India. Uploading the IP to VirusTotal to determine if the community had reported the IP as suspicious/malicious I did not find any corroborating evdience.<br/>
<img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/d3b44c29-15ce-4130-b9bd-1faf1d02c6bc"  alt="SSH Brute-Force Analysis"/>
<img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/564f1ec0-9464-41d4-8d8d-7e7ff97cd847"  alt="SSH Brute-Force Analysis"/>
<br />
<br />
Since that was a dead end, I went back to viewing the logs and pivoted to the IP and used grep "accpeted" followed by the attacker's IP to determine if the attacker successfully login into any accounts and found it logged into the account "root" twice and the account "cyberjunkie" once. Furthermore, I used the grep command on auth.log looking for root and saw that the first SSH session was opened on March 6 and lasted for less than a second, while the second session lasted 279 seconds. <br/>
<img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/e5fa03d0-87df-43c4-8515-e243102aa7d2)"  alt="SSH Brute-Force Analysis"/>
<img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/c610463e-d25d-4281-9afc-ed22662568f8"  alt="SSH Brute-Force Analysis"/>
<img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/b56245b9-6ccc-4618-ab93-e2b04cbc002c"  alt="SSH Brute-Force Analysis"/>
<br />
<br />
Directly after the second session was closed, the following logs show the user "cyberjunkie" using commands. I used the grep command for "cyberjunkie" in auth.log and found that the user was added on Mar 6 06:34:18 which was when during the second session when the attacker was logged in as root. Further evidence linking the the newly created account "cyberjunkie" to the attacker is when I opened the wtmp logs using utmpdump, the session of cyberjunkie has the same IP of 65[.]2[.]161[.]68 as the user logged in as root which was the same IP that conducted the Brute-Force Attack. The logs of "cyberjunkie" show two dangerous attacks, one using "sudo cat /etc/shadow" which is to view passwords, and the other command was a curl commmand to a shell file. <br/>
<img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/79473ac9-3802-442a-9f6d-e98158248229"  alt="SSH Brute-Force Analysis"/>
<img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/d4a01f05-65d9-46ee-82f8-127c4754d889"  alt="SSH Brute-Force Analysis"/>
 <img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/2f4014d4-445e-4192-a495-bde8243fb759"  alt="SSH Brute-Force Analysis"/>
<br />
<br />
The shell file that was curled downloaded a reverse shell for persistance. The Mitre ATT&CK that this attack would fall under is brute force for intial access and create account for persistence<br/>
<img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/4220e4aa-1af2-4cb2-b466-5c7ef6f2cab7"  alt="SSH Brute-Force Analysis"/>
 <img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/054e2172-6527-499f-a195-7f7d227d206a"  alt="SSH Brute-Force Analysis"/>
 <img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/166843aa-faad-4aa1-b96f-3347f25b1057"  alt="SSH Brute-Force Analysis"/>
<br />
<br />
<br />
<h2>Thoughts</h2>
This lab/CTF was good practice using basic Linux command-line tools to investigate an incident. While it is probably not practical to analyze logs on the command line and would likely use an EDR or SIEM solution with a dashboard GUI to view them, having the skills and ability to use the Linux command line to uncover evidence of an attack is an important skill I have developed. This process was fairly straightforward as the sample log was not very big and the attacker did not use any obfuscation techniques to avoid detection. Moreover, using the cat command on /etc/shadow is an enormous red flag. From what I have learned in cybersecurity thus far, it is extremely rare for someone to try to view that file without malicious intent, so any log of it happening likely is the result of an attack. Overall, this lab proved to be beneficial practice despite not being complex.


<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>


<br />

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
