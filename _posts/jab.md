sup:: [[Home]]
tags:: #map 

# jab 🗃



Let's get started...

---
### Steps
1.  **Start your vpn**
	Go to the directory where your downloaded vpn and start the vpn as shown below
	
![[Pasted image 20240114130711.png]]

Click on the giant purple button to download it for your OS (it should auto-detect what you have). Once it is downloaded, open the file to install and YAYAYAYAYAY!
Your computer now has Obsidian.

2. Run a scan with nmap:

![[Pasted image 20240225084016.png]]

![[Pasted image 20240225191821.png]]

3. So, after the nmap scan, i made some research due to being overwhelmed with so much ports and not knowing which one to start with. I found out about **Port 5222 (Jabber/XMPP)** which is an instant messaging protocol.

This might help us to find custom or default credentials that can be used to authenticate, or there could be interesting information exchanges happening over this protocol.

So, we can go ahead and explore the Jabber/XMPP service for potential chat logs, misconfigurations, or use it as an entry point if credentials can be obtained.

5. The IP was not resolving so I had to add it to the /etc/hosts but still wasn't resolving:

![[Pasted image 20240225084752.png]]

6. Next, thing was to install Pidgin which is a messaging client for Jaber/XMPP:

![[Pasted image 20240225181429.png]]

7. Launch Pidgin:

![[Pasted image 20240225182658.png]]

![[Pasted image 20240227072005.png]]

![[Pasted image 20240227073151.png]]

![[Pasted image 20240225191645.png]]

9. Click on the Tools tab and enable the Xmpp console and service discovery extensions:

10. Next, Go to Tools > Service Discovery > Browse to search for the available services:

![[Pasted image 20240227075327.png]]

11. Add them to the /etc/hosts file:

![[Pasted image 20240227080104.png]]

12. Next, go the Tools tab > XMPP Console and launch it:



14. Next, go to Accounts tab > user@jab.htb > Search for users

![[Pasted image 20240227082116.png]]

13. Go to Tools > XMIn the windows dialogue, type in * for wildcard and then click Ok

![[Pasted image 20240227082705.png]]

![[Pasted image 20240227082845.png]]

14. Next, we'll extract the names from the saved xml file

![[Pasted image 20240227110844.png]]

15. Next, is ASREP Roasting:

![[Pasted image 20240228081700.png]]

16. Next, we'll crack the hashes in the saved hashed file:

![[Pasted image 20240227123127.png]]

![[Pasted image 20240227132913.png]]

![[Pasted image 20240227133152.png]]

17. Since, it showed one password was cracked we have to reveal it from the output:

![[Pasted image 20240227133529.png]]

18. Add the found credentials to the Pidgin application:

![[Pasted image 20240227141700.png]]

19. Now, we'll repeat same process as we did for the first user in searching for services:

![[Pasted image 20240227142411.png]]

20. We could see a new room found:

![[Pasted image 20240227144603.png]]

21. We'll try to join the room found:

![[Pasted image 20240227145053.png]]


22. After clicking on the Join button, we were able to access the chat room. In the chat, I saw the cracked password for svc_openfire:

![[Pasted image 20240227145351.png]]

![[Pasted image 20240227151918.png]]

23. Got an encoded powershell reverse shell from this site and setup a listener using the same IP and port number:

![[Pasted image 20240227161538.png]]

![[Pasted image 20240227161159.png]]

24. Next, i entered in the encoded command into the dcompexec command:


But had some issues with impacket due to out of date, so i installed another one using another method:

![[Pasted image 20240227183149.png]]

![[Pasted image 20240227183251.png]]

![[Pasted image 20240227183400.png]]


26. Since port 88, lets try using kerbrute to enumerate for more users on the domain:

![[Pasted image 20240225111352.png]]

![[Pasted image 20240225114423.png]]

2024/02/25 09:14:11 >  [!] lacy@jab.htb - failed to communicate with KDC. Attempts made with UDP (error sending to a KDC: error sneding to jab.htb:88: sending over UDP failed to 10.10.11.4:88: read udp 10.10.14.160:40647->10.10.11.4:88: i/o timeout) and then TCP (error in getting a TCP connection to any of the KDCs)
2024/02/25 09:14:11 >  [!] lalalala@jab.htb - failed to communicate with KDC. Attempts made with UDP (error sending to a KDC: error sneding to jab.htb:88: sending over UDP failed to 10.10.11.4:88: read udp 10.10.14.160:54510->10.10.11.4:88: i/o timeout) and then TCP (error in getting a TCP connection to any of the KDCs)
2024/02/25 09:14:12 >  Done! Tested 13254 usernames (103 valid) in 504.201 seconds
