sup:: [[Home]]
tags:: #map 

# Monitored 🗃

![[Pasted image 20240220044459.png]]

Let's get started...

---
### Steps
1.  **Start your vpn**
	Go to the directory where your downloaded vpn and start the vpn as shown below
	
![[Pasted image 20240114130711.png]]

Click on the giant purple button to download it for your OS (it should auto-detect what you have). Once it is downloaded, open the file to install and YAYAYAYAYAY!
Your computer now has Obsidian.

2. Run a scan with nmap

![[Pasted image 20240220045511.png]]

3. We check the http://10.10.11.13 and found the web page since port 80 was open:

![[Pasted image 20240220045835.png]]

4. Navigating to /robots.txt, we found same directories as seen in Nmap result:

![[Pasted image 20240220050230.png]]

5. Did some directory bruteforcing with dirsearch:

a.
![[Pasted image 20240220050943.png]]
![[Pasted image 20240220051048.png]]

b. 	used ffuf for fuzzing and found similar but less compared to what I got from dirsearch:

![[Pasted image 20240220052435.png]]

		c. Next, again ran ffuf

ffuf -w SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://internal.analysis.htb/employees/FUZZ -e .php,.html,.js

![[Pasted image 20240125193911.png]]


6. Next, we accessed the /administrator page

![[Pasted image 20240220053441.png]]

7. After doing some research over jomla 4.2 I found an exploit for it:

![[Pasted image 20240220212206.png]]

8.  We need to install the required ruby gems first before proceeding with the exploit:

![[Pasted image 20240220212505.png]]

9. I copied the exploit into a file using a text editor:

![[Pasted image 20240220212804.png]]

10. I ran the exploit against domain(http://office.htb) or IP (http://10.10.11.3):

![[Pasted image 20240220213500.png]]

NOTE: I also used this github repo for reference:
https://github.com/Acceis/exploit-CVE-2023-23752?tab=readme-ov-file


11. Since port 88, lets try using kerbrute to enumerate for more users on the domain:

![[Pasted image 20240220230054.png]]


12. Given that we already found a password (H0lOgrams4reTakIng0Ver754!) from the Joomla exploit, we can use this password in a password spraying attack against the list of valid usernames you identified with `kerbrute`. The goal is to see if any of these accounts use this password. 

Since we found some valid usernames, we will make a wordlist of them in a text file:

Typically, SMB does not require email-format usernames (`user@domain`). Instead, use just the username part without the domain (`administrator`, `ewhite`, etc.), unless you are specifically targeting a setup that requires full UPN format. The domain is already specified with the `-d office.htb` flag, so it might be redundant or incorrect to include `@office.htb` in the usernames.

![[Pasted image 20240220234931.png]]

13. Next, we'll use CrackMapExec for SMB password spraying:

Note: CrackMapExec is specifically designed to work with Windows environments and might handle SMB interactions more gracefully.

SMB (Server Message Block) is often targeted in password spraying and other types of attacks because of:
**Widespread Use**: SMB is commonly used in Windows environments for file sharing, printer sharing, and access to remote Windows services. Because of its ubiquity in corporate networks, it often becomes a target for attackers looking to gain access to network resources.

Notice: that the last line of CrackMapExec output of the last line that does not end with `STATUS_LOGON_FAILURE` indicating the authentication or login attempt was successful:

![[Pasted image 20240221000640.png]]

![[Pasted image 20240220233842.png]]

16. **Access and Explore SMB Shares**: We will use `smbclient` to connect to SMB shares accessible by the `dwolfe` account. This will allow us to explore available files and directories that might contain sensitive information or further network insights.

The output from smbclient shows that you've successfully listed the SMB shares available on office.htb using the credentials for the user dwolfe

![[Pasted image 20240221003126.png]]

18. Let's try to connect to the SOC Analysis share. After the connection was successful notice a pcap file in the directory:

![[Pasted image 20240221010928.png]]

19. Let's try to download the pcap file:

![[Pasted image 20240221011436.png]]

20. Analyzing the pcap file with wireshark. Since, most of the traffic is encrypted will filter for smb:

![[Pasted image 20240223143455.png]]


21. Kerberos Pre-Authentication data hash ($krb5pa$18$...) using mode 19900 (Kerberos 5, etype 18, Pre-Auth), which is correct for the eTYPE-AES256-CTS-HMAC-SHA1-96 and save it to a file:

![[Pasted image 20240223152553.png]]

22. Now we'll attempt to crack the hash using hashcat:

![[Pasted image 20240223154234.png]]

![[Pasted image 20240223154012.png]]

23. Going back to the login page since we were able to crack the password for tstark who seems to be an administrator on the system. We will attempt to login with what we found and see if it works:

![[Pasted image 20240223155458.png]]

24. We were able to successfully login to the dashboard as an admin:

![[Pasted image 20240223155737.png]]

25. Next, click on the System > Administrator Templates

![[Pasted image 20240223160405.png]]

20 . Click on the System tab and navigate to Template and click on Administrator Templates:

![[Pasted image 20240222212202.png]]

26. Click on the Template:

![[Pasted image 20240222212503.png]]

22. Select a template you want to edit, in my case I will choose the index.php

![[Pasted image 20240222212917.png]]

24. Set  up a listener:

![[Pasted image 20240222221330.png]]

25. I replaced the index.php code with PHP Ivan Sincek code. Then clicked on Save button

![[Pasted image 20240223005643.png]]

![[Pasted image 20240223005803.png]]

26. Then I got a reverse shell:

![[Pasted image 20240223005256.png]]

27. I downloaded RunasCs file so as to send it to the target host:

RunasCs is a utility to run specific processes with different permissions than the user’s current logon provides using explicit credentials.

![Image](https://media.discordapp.net/attachments/1197178919368015945/1210500737872953404/image.png?ex=65eac9a9&is=65d854a9&hm=20af0593ebddfeebcb828b6138287dbc405108617965d462f5b345114e100b58&=&format=webp&quality=lossless&width=1135&height=504)

28. Before transfering the RunasCs.exe file, I started a python server to server the file

![[Pasted image 20240223203333.png]]

29. Next, is to download the file using certutil tool in the \C\Wiindows\Temp directory:

![[Pasted image 20240223203753.png]]

![[Pasted image 20240223203922.png]]

30. Setup  another listener to catch another reverse shell when we execute RunasCs.exe:

![[Pasted image 20240223204941.png]]

31. Now, let's run the RunasCs.exe to gain a reverse shell:
The command uses RunasCs.exe to execute PowerShell as the user tstark with the password playboy69, creating a reverse shell to your machine on port 4444

![[Pasted image 20240223205506.png]]

![[Pasted image 20240223205414.png]]

32. We can see that we got a shell for tstark:

![[Pasted image 20240223210139.png]]

33. Navigating to tstark Desktop directory we could find the user flag

![[Pasted image 20240223210614.png]]

34. From here, navigate to the \xampp\htdocs\internal directory we find a resume.php file:

![[Pasted image 20240223221711.png]]

35. Now, back to our Kali Linux machine. Git clone this exploit:

![[Pasted image 20240223231745.png]]

https://github.com/elweth-sec/CVE-2023-2255
![[Pasted image 20240223232712.png]]

36. In the CVE-2023-2255 directory, we will have to generate a file called exploit.odt to for exploiting CVE-2023-2255 vulnerability by embedding a command to download a web shell:

![[Pasted image 20240224021305.png]]

37.  Launched another http server to serve the payload in the same directory where the exploit is

![[Pasted image 20240224001542.png]]

38.  Moved to the Temp directory to transfer the exploit:

![[Pasted image 20240224022424.png]]

39. Downloaded chisel for both Linux and Windows binaries:

![[Pasted image 20240224073949.png]]

![[Pasted image 20240224074336.png]]

40. Unzip both files:

![[Pasted image 20240224074815.png]]

41. Transfer the windows chisel exe onto the Windows target while our server is still running:

![[Pasted image 20240224081036.png]]

![[Pasted image 20240224081125.png]]

42. Make the Linux chisel binary executable:

![[Pasted image 20240224081701.png]]

43. Back to the web_account, we figured out that it has some privileges to download the .odt file:

![[Pasted image 20240224090143.png]]

![[Pasted image 20240224090247.png]]