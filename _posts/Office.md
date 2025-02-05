sup:: [[Home]]
tags:: #map 

# Monitored 🗃

![Pasted image 20240220044459](https://github.com/user-attachments/assets/672dbef7-9b90-4789-89bc-508209157856)


Let's get started...

---
### Steps
1.  **Start your vpn**
	Go to the directory where your downloaded vpn and start the vpn as shown below
	
![[Pasted image 20240114130711.png]]

Click on the giant purple button to download it for your OS (it should auto-detect what you have). Once it is downloaded, open the file to install and YAYAYAYAYAY!
Your computer now has Obsidian.

2. Run a scan with nmap

![Pasted image 20240220045511](https://github.com/user-attachments/assets/073c08aa-9f47-4220-9935-76a66edb9c1b)


3. We check the http://10.10.11.13 and found the web page since port 80 was open:

![Pasted image 20240220045835](https://github.com/user-attachments/assets/ccf45375-6e30-4986-b290-5ccee4bb8209)


4. Navigating to /robots.txt, we found same directories as seen in Nmap result:

![[Pasted image 20240220050230.png]]

5. Did some directory bruteforcing with dirsearch:

a.
![Pasted image 20240220050943](https://github.com/user-attachments/assets/39bc8c52-f448-48ac-ac11-4d9dec013fa5)

![Pasted image 20240220051048](https://github.com/user-attachments/assets/c9768190-7449-40fc-b1a4-04945e2cfa5b)


b.	used ffuf for fuzzing and found similar but less compared to what I got from dirsearch:

![Pasted image 20240220052435](https://github.com/user-attachments/assets/07c8c66a-f214-491d-861f-7aa269705a09)


c. Next, again ran ffuf

ffuf -w SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://internal.analysis.htb/employees/FUZZ -e .php,.html,.js

![Pasted image 20240125193911](https://github.com/user-attachments/assets/8545e493-52d5-486d-9319-3118180a6d80)


6. Next, we accessed the /administrator page

![Pasted image 20240220053441](https://github.com/user-attachments/assets/707be4cc-a09c-4d77-a0f0-7fa112190fd4)


7. After doing some research over joomla 4.2 I found an exploit for it:

![Pasted image 20240220212206](https://github.com/user-attachments/assets/e393956c-61bb-485c-8be2-7345a510abee)


8.  We need to install the required ruby gems first before proceeding with the exploit:

![Pasted image 20240220212505](https://github.com/user-attachments/assets/351ad27c-33c9-4518-85d6-f119a577dcd7)


9. I copied the exploit into a file using a text editor:

![Pasted image 20240220212804](https://github.com/user-attachments/assets/2225180c-906b-4ce0-ba1e-31bdf95d1008)


10. I ran the exploit against domain(http://office.htb) or IP (http://10.10.11.3):

![Pasted image 20240220213500](https://github.com/user-attachments/assets/b9fca847-b955-4b19-8b26-87d902085e54)


NOTE: I also used this github repo for reference:
https://github.com/Acceis/exploit-CVE-2023-23752?tab=readme-ov-file


11. Since port 88, lets try using kerbrute to enumerate for more users on the domain:

![Pasted image 20240220230054](https://github.com/user-attachments/assets/dd72e21b-1fc0-4d7d-a31c-4496542ed4a1)



12. Given that we already found a password (H0lOgrams4reTakIng0Ver754!) from the Joomla exploit, we can use this password in a password spraying attack against the list of valid usernames you identified with `kerbrute`. The goal is to see if any of these accounts use this password. 

Since we found some valid usernames, we will make a wordlist of them in a text file:

Typically, SMB does not require email-format usernames (`user@domain`). Instead, use just the username part without the domain (`administrator`, `ewhite`, etc.), unless you are specifically targeting a setup that requires full UPN format. The domain is already specified with the `-d office.htb` flag, so it might be redundant or incorrect to include `@office.htb` in the usernames.

![Pasted image 20240220234931](https://github.com/user-attachments/assets/3b239957-3713-4678-887a-ebdfd4c8ef18)


13. Next, we'll use CrackMapExec for SMB password spraying:

Note: CrackMapExec is specifically designed to work with Windows environments and might handle SMB interactions more gracefully.

SMB (Server Message Block) is often targeted in password spraying and other types of attacks because of:
**Widespread Use**: SMB is commonly used in Windows environments for file sharing, printer sharing, and access to remote Windows services. Because of its ubiquity in corporate networks, it often becomes a target for attackers looking to gain access to network resources.

Notice: that the last line of CrackMapExec output of the last line that does not end with `STATUS_LOGON_FAILURE` indicating the authentication or login attempt was successful:

![Pasted image 20240221000640](https://github.com/user-attachments/assets/f5c19aab-159d-42f0-8d7e-50e548ad59d9)


![Pasted image 20240220233842](https://github.com/user-attachments/assets/ac0243a7-0cde-4ad0-9b28-6da4dab1cbe6)


16. **Access and Explore SMB Shares**: We will use `smbclient` to connect to SMB shares accessible by the `dwolfe` account. This will allow us to explore available files and directories that might contain sensitive information or further network insights.

The output from smbclient shows that you've successfully listed the SMB shares available on office.htb using the credentials for the user dwolfe

![Pasted image 20240221003126](https://github.com/user-attachments/assets/3efe7c10-77f8-4063-b2b6-9859a9310127)


18. Let's try to connect to the SOC Analysis share. After the connection was successful notice a pcap file in the directory:

![Pasted image 20240221010928](https://github.com/user-attachments/assets/6401c74d-33f7-4ec3-848a-fddeed18c0bf)

19. Let's try to download the pcap file:

![Pasted image 20240221011436](https://github.com/user-attachments/assets/0b7d9e81-d87b-427a-abc8-4b28aabcf7bf)


20. Analyzing the pcap file with wireshark. Since, most of the traffic is encrypted will filter for smb:

![Pasted image 20240223143455](https://github.com/user-attachments/assets/57fe7715-0498-4737-8c71-7bcb69dbb545)



21. Kerberos Pre-Authentication data hash ($krb5pa$18$...) using mode 19900 (Kerberos 5, etype 18, Pre-Auth), which is correct for the eTYPE-AES256-CTS-HMAC-SHA1-96 and save it to a file:

![Pasted image 20240223152553](https://github.com/user-attachments/assets/94bec87b-72b6-4539-8383-65d4757a7a5b)


22. Now we'll attempt to crack the hash using hashcat:

![Pasted image 20240223154234](https://github.com/user-attachments/assets/f6e1ab5a-f005-4596-9345-d9afaec44344)


![Pasted image 20240223154012](https://github.com/user-attachments/assets/13ec2ffa-3626-4518-ba8c-2c4d23ef32ae)


23. Going back to the login page since we were able to crack the password for tstark who seems to be an administrator on the system. We will attempt to login with what we found and see if it works:

![Pasted image 20240223155458](https://github.com/user-attachments/assets/51c77f22-03e9-40b5-bc17-b8099bb430be)


24. We were able to successfully login to the dashboard as an admin:

![Pasted image 20240223155737](https://github.com/user-attachments/assets/dcf82a77-4623-44f8-a76a-dd0b97597455)


25. Next, click on the System > Administrator Templates

![Pasted image 20240223160405](https://github.com/user-attachments/assets/596abf39-bbba-46cd-8bda-722ed481a4c1)


20 . Click on the System tab and navigate to Template and click on Administrator Templates:

!![Pasted image 20240222212202](https://github.com/user-attachments/assets/6a4c3d9d-b2df-462b-8ddf-4de17d9aed7d)


26. Click on the Template:

![Pasted image 20240222212503](https://github.com/user-attachments/assets/255c4c4b-8eaf-47b3-88ee-748991e20cca)


22. Select a template you want to edit, in my case I will choose the index.php

![Pasted image 20240222212917](https://github.com/user-attachments/assets/1a2fb18c-176a-46f4-bf12-420d60e75cfe)


24. Set  up a listener:

![Pasted image 20240222221330](https://github.com/user-attachments/assets/543c223c-e003-4fae-b3b6-1b2f3780244c)


25. I replaced the index.php code with PHP Ivan Sincek code. Then clicked on Save button

![Pasted image 20240223005643](https://github.com/user-attachments/assets/d5f9822e-ab74-4bc8-9399-622af9abecbe)


![Pasted image 20240223005803](https://github.com/user-attachments/assets/be179e63-63fd-40ad-9145-a18140358c41)


26. Then I got a reverse shell:

![Pasted image 20240223005256](https://github.com/user-attachments/assets/3e2e26b2-b671-4f0e-bf10-efe7caf5e12e)


27. I downloaded RunasCs file so as to send it to the target host:

RunasCs is a utility to run specific processes with different permissions than the user’s current logon provides using explicit credentials.

![Image](https://media.discordapp.net/attachments/1197178919368015945/1210500737872953404/image.png?ex=65eac9a9&is=65d854a9&hm=20af0593ebddfeebcb828b6138287dbc405108617965d462f5b345114e100b58&=&format=webp&quality=lossless&width=1135&height=504)

28. Before transfering the RunasCs.exe file, I started a python server to server the file

![Pasted image 20240223203333](https://github.com/user-attachments/assets/795b4162-83a0-41bb-b197-6bbf9fe31ef1)


29. Next, is to download the file using certutil tool in the \C\Wiindows\Temp directory:

![Pasted image 20240223203753](https://github.com/user-attachments/assets/7854a34a-8f5b-4582-9d99-5a6ac88d1227)


![Pasted image 20240223203922](https://github.com/user-attachments/assets/d19198ce-2ebd-408e-8259-8ad50cf735ed)


30. Setup  another listener to catch another reverse shell when we execute RunasCs.exe:

![Pasted image 20240223204941](https://github.com/user-attachments/assets/b6fb8339-3488-457b-9e7a-91df54042c17)


31. Now, let's run the RunasCs.exe to gain a reverse shell:
The command uses RunasCs.exe to execute PowerShell as the user tstark with the password playboy69, creating a reverse shell to your machine on port 4444

![Pasted image 20240223205506](https://github.com/user-attachments/assets/9788041c-cd73-4f5a-9fc1-24e858d06ddb)


![Pasted image 20240223205414](https://github.com/user-attachments/assets/0674c836-3286-420a-aba6-33c466b39730)


32. We can see that we got a shell for tstark:

![Pasted image 20240223210139](https://github.com/user-attachments/assets/c8ffa766-2142-46b9-9101-e73911438874)


33. Navigating to tstark Desktop directory we could find the user flag

![Pasted image 20240223210614](https://github.com/user-attachments/assets/7670439e-a56c-4d98-bade-ef72525d1861)


34. From here, navigate to the \xampp\htdocs\internal directory we find a resume.php file:

![Pasted image 20240223221711](https://github.com/user-attachments/assets/d9599cc5-58aa-4be1-924b-17242be0416f)


35. Now, back to our Kali Linux machine. Git clone this exploit:

![Pasted image 20240223231745](https://github.com/user-attachments/assets/c8f55093-8e97-4503-8b58-debbfd45f2da)


https://github.com/elweth-sec/CVE-2023-2255
![Pasted image 20240223232712](https://github.com/user-attachments/assets/66e185f6-13e4-465c-8d3b-db5340de6965)


36. In the CVE-2023-2255 directory, we will have to generate a file called exploit.odt to for exploiting CVE-2023-2255 vulnerability by embedding a command to download a web shell:

![Pasted image 20240224021305](https://github.com/user-attachments/assets/861407af-30c1-4092-86ba-1f2ed2d13c2c)

37.  Launched another http server to serve the payload in the same directory where the exploit is

![Pasted image 20240224001542](https://github.com/user-attachments/assets/6943dcb0-0f47-46c9-8948-26d24a7f2881)


38.  Moved to the Temp directory to transfer the exploit:

![Pasted image 20240224022424](https://github.com/user-attachments/assets/4f713ec1-8d81-44a8-b419-560d95f29585)


39. Downloaded chisel for both Linux and Windows binaries:

![Pasted image 20240224073949](https://github.com/user-attachments/assets/65e2a682-0505-4670-a4d7-d6d57973f3fb)


![Pasted image 20240224074336](https://github.com/user-attachments/assets/599e2c63-1807-4467-9f29-2bfaa9093f0e)

40. Unzip both files:

![Pasted image 20240224074815](https://github.com/user-attachments/assets/b02cf2de-eea7-4610-9a52-d397fbc80b11)

41. Transfer the windows chisel exe onto the Windows target while our server is still running:

![Pasted image 20240224081036](https://github.com/user-attachments/assets/2df754e4-f359-4bed-b3f8-07e88c2f43fe)


![Pasted image 20240224081125](https://github.com/user-attachments/assets/c550799e-ca34-4796-99ba-8f34cc42fa1c)


42. Make the Linux chisel binary executable:

![Pasted image 20240224081701](https://github.com/user-attachments/assets/bba47408-3a45-4b88-83b9-4191adbb778b)


43. Back to the web_account, we figured out that it has some privileges to download the .odt file:

![Pasted image 20240224090143](https://github.com/user-attachments/assets/fed87515-e1d2-4204-abb7-63f7103ee470)


![Pasted image 20240224090247](https://github.com/user-attachments/assets/5714ab7d-197e-41ce-9a2e-fdd9ec944ec3)

