<h1>Brim IDS/Packet Analysis Lab </h1>


<h2>Description</h2>
Text
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
The log files to examine, auth.log posses authentication logs failed and sucecessful and wtmp logs also shows sucessful log session logins with timestamp and IP address.<br/>
<img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/71385460-bc58-4534-8768-3fd62f727794)"  alt="SSH Brute-Force Analysis"/>
<br />
<br />
Using the cat command on auth.log to output the entire log, I immediatly notice suspicous activcity with a user "cyberjunkie being created and given sudo or root priviliges, likely confirming a sucessful attack occured. However, now it is time to find out how it began and if there are other indicators of compromise or attack.<br/>
<img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/d8aaddcd-0ddc-4e01-bb1a-5432854de865"  alt="SSH Brute-Force Analysis"/>
<br />
<br />
I begin using some CLI-fu (different command line tools) to try and uncover mroe information of the attack, starting by see if there was any suspicious failed login logs indicating a potentional password spraying or brute force attack. And it appeared that there was a brute force attack. The attacker first targeted an account "admin" however, the user did not exist and then began targeting the accounts "root" and "backup" which were accounts. <br/>
<img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/c9672fda-4882-4c89-ade5-26e98a8ac7fd"  alt="SSH Brute-Force Analysis"/>
<br />
<br />
I first sorted for the logs that with the string "Failed Password" while executing invalid to only show the logs that were generated from failed logins to active accounts using the grep command with the -i for case insesnsitivity and -v to exclude. I then added a cut command to the previous command to only pull out the the date, time, account, and IP of the attack as well as sorting and counting the attempts for easier viewing that showing the backup account has 9 failed login attemps and the root account had 6 failed login attempts. <br/>
<img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/8e42d0a6-9309-4930-a1ed-eb45965a4b83"  alt="SSH Brute-Force Analysis"/>
<img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/e139ab5d-9747-4b6b-92f3-21878916a0e2"  alt="SSH Brute-Force Analysis"/>
<br />
<br />
It is now clear that there is a single IP that has conducted a brute-force attack against multiople accounts I began to investigate teh IP to see if there is any informtion I can find out about it. On the command line I used the curl command to access the website ipinfo[.]io with the IP that conducted the attack and found that it is an amazon webserver from Mumbai, India. Uploading the IP to VirusTotal to determine if the community had reproted the IP as suspicious I did not find any corroborating evdience.<br/>
<img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/d3b44c29-15ce-4130-b9bd-1faf1d02c6bc"  alt="SSH Brute-Force Analysis"/>
<img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/564f1ec0-9464-41d4-8d8d-7e7ff97cd847"  alt="SSH Brute-Force Analysis"/>
<br />
<br />
Since that was a dead end, I went back to viewing the logs and pivoted to the IP and used grep "accpeted" followed by the attacker's IP to determine if the attacker successfully login into any accounts and found it logged into the account "root" twice and the account "cyberjunkie" once. Furthermore, I used the grep command on auth.log looking for root and see that the first SSH session was opened on March 6 and lasted for less than a second, while the second session lasted 279 seconds. <br/>
<img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/e5fa03d0-87df-43c4-8515-e243102aa7d2)"  alt="SSH Brute-Force Analysis"/>
<img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/c610463e-d25d-4281-9afc-ed22662568f8"  alt="SSH Brute-Force Analysis"/>
<img src="https://github.com/KirkDJohnson/SSH-Brute-Force-Log-Analysis-Lab/assets/164972007/b56245b9-6ccc-4618-ab93-e2b04cbc002c"  alt="SSH Brute-Force Analysis"/>
<br />
<br />
Directly after the second session was closed, the following logs show the user "cyberjunkie" using commands. <br/>
<img src=""  alt="SSH Brute-Force Analysis"/>
<img src=""  alt="SSH Brute-Force Analysis"/>
<br />
<br />
Text<br/>
<img src=""  alt="SSH Brute-Force Analysis"/>
<br />
<br />
Text<br/>
<img src=""  alt="SSH Brute-Force Analysis"/>
<br />
<br />
Text<br/>
<img src=""  alt="SSH Brute-Force Analysis"/>
<br />
<br />
Text<br/>
<img src=""  alt="SSH Brute-Force Analysis"/>
<br />
<br />
Text<br/>
<img src=""  alt="SSH Brute-Force Analysis"/>
<br />
<br />


<br />
<h2>Thoughts</h2>
Text


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
<h2>Thoughts</h2>
Text

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
