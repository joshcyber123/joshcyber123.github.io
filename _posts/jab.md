sup:: [[Home]]
tags:: #map 

# jab 🗃



Let's get started...

---
### Steps
1.  **Start your vpn**
	Go to the directory where your downloaded vpn and start the vpn as shown below
	

2. Run a scan with nmap:

![Pasted image 20240225084016](https://github.com/user-attachments/assets/82a22aaa-1c10-4d71-9d56-e7556fbbcfaf)


![Pasted image 20240225191821](https://github.com/user-attachments/assets/d05e9d2c-5aed-4366-983f-ccba03cd3362)


3. So, after the nmap scan, i made some research due to being overwhelmed with so much ports and not knowing which one to start with. I found out about **Port 5222 (Jabber/XMPP)** which is an instant messaging protocol.

This might help us to find custom or default credentials that can be used to authenticate, or there could be interesting information exchanges happening over this protocol.

So, we can go ahead and explore the Jabber/XMPP service for potential chat logs, misconfigurations, or use it as an entry point if credentials can be obtained.

5. The IP was not resolving so I had to add it to the /etc/hosts but still wasn't resolving:

![Pasted image 20240225084752](https://github.com/user-attachments/assets/4eaf0e41-9768-4ee3-8bf1-c4cb28879879)


6. Next, thing was to install Pidgin which is a messaging client for Jaber/XMPP:

![Pasted image 20240225181429](https://github.com/user-attachments/assets/45a7a23f-b82d-4ea7-908e-530204338990)


7. Launch Pidgin:

![Pasted image 20240225182658](https://github.com/user-attachments/assets/bc24eba0-5409-4d93-9e6b-030359513d11)

![Pasted image 20240227072005](https://github.com/user-attachments/assets/e7585a13-8db9-4d69-b688-89f0e6862630)


![Pasted image 20240227073151](https://github.com/user-attachments/assets/0a38e7ff-6c1b-4d00-8a5a-07e8b0e5d3d5)


![Pasted image 20240225191645](https://github.com/user-attachments/assets/9cc6401c-251e-4e36-8ce4-03000c601c41)


9. Click on the Tools tab and enable the Xmpp console and service discovery extensions:

10. Next, Go to Tools > Service Discovery > Browse to search for the available services:

![Pasted image 20240227075327](https://github.com/user-attachments/assets/5601ef6f-cce1-4c05-a064-f9ed511ff133)


11. Add them to the /etc/hosts file:

![Pasted image 20240227080104](https://github.com/user-attachments/assets/d0629090-06bf-4ca9-9ace-e14878788836)


12. Next, go the Tools tab > XMPP Console and launch it:



14. Next, go to Accounts tab > user@jab.htb > Search for users

![Pasted image 20240227082116](https://github.com/user-attachments/assets/cc41c0bf-ace6-444d-9f86-2063c7145873)


13. Go to Tools > XMIn the windows dialogue, type in * for wildcard and then click Ok

![Pasted image 20240227082705](https://github.com/user-attachments/assets/9867361d-5257-4d3f-9d76-3de9df2fee7e)


![Pasted image 20240227082845](https://github.com/user-attachments/assets/69294bcc-b549-4659-ad06-edba7bea3e1c)


14. Next, we'll extract the names from the saved xml file

![Pasted image 20240227110844](https://github.com/user-attachments/assets/677f5f01-773f-498a-8c59-c305e939f2b6)


15. Next, is ASREP Roasting:

![Pasted image 20240228081700](https://github.com/user-attachments/assets/421aa3bd-7a92-4ac4-b12b-23709e971271)


16. Next, we'll crack the hashes in the saved hashed file:

![Pasted image 20240227123127](https://github.com/user-attachments/assets/7e7e6e4e-f7a9-4e57-98d9-b7fc604edd58)

![Pasted image 20240227132913](https://github.com/user-attachments/assets/106df583-2b27-48c9-954e-95fd7522f201)


![Pasted image 20240227133152](https://github.com/user-attachments/assets/b8a01b9f-0517-4005-b55f-d2bf4de05fd2)


17. Since, it showed one password was cracked we have to reveal it from the output:

![Pasted image 20240227133529](https://github.com/user-attachments/assets/76692ede-b467-434b-84b3-2ef8c53b574b)

18. Add the found credentials to the Pidgin application:

![Pasted image 20240227141700](https://github.com/user-attachments/assets/e44ab135-44d5-4a60-8d38-db2b816ff5ee)


19. Now, we'll repeat same process as we did for the first user in searching for services:

![Pasted image 20240227142411](https://github.com/user-attachments/assets/a60395b1-ad37-4f75-88a6-fea39e7ba293)


20. We could see a new room found:

![Pasted image 20240227144603](https://github.com/user-attachments/assets/1d299958-5865-463d-9799-f0d775755a0e)


21. We'll try to join the room found:

![Pasted image 20240227145053](https://github.com/user-attachments/assets/53687ea9-92b3-4243-90a7-1b97ced5dd28)


22. After clicking on the Join button, we were able to access the chat room. In the chat, I saw the cracked password for svc_openfire:

![Pasted image 20240227145351](https://github.com/user-attachments/assets/1b7f916a-316b-43b9-8d93-6b2afec93699)


![Pasted image 20240227151918](https://github.com/user-attachments/assets/f12445a1-f1ce-4844-8518-1c90aad681b4)


23. Got an encoded powershell reverse shell from this site and setup a listener using the same IP and port number:

![Pasted image 20240227161538](https://github.com/user-attachments/assets/bb57bd79-9444-415d-ac72-c66d1c9a13de)


![Pasted image 20240227161159](https://github.com/user-attachments/assets/1092d2c5-a7b2-46cf-99ac-1629f322ca15)


24. Next, i entered in the encoded command into the dcompexec command:


But had some issues with impacket due to out of date, so i installed another one using another method:

![Pasted image 20240227183149](https://github.com/user-attachments/assets/e3ed0974-12d2-45d3-ae47-fdea8ed52804)


![Pasted image 20240227183251](https://github.com/user-attachments/assets/d22b954a-e90d-4f0f-be03-58165d8de14a)


![Pasted image 20240227183400](https://github.com/user-attachments/assets/29cb0d42-7f2b-4666-a6c6-ef4f39e10286)


26. Since port 88, lets try using kerbrute to enumerate for more users on the domain:

![Pasted image 20240225111352](https://github.com/user-attachments/assets/055184f9-3efc-4841-beb0-74c264e1192b)


![Pasted image 20240225114423](https://github.com/user-attachments/assets/548a75f2-b098-4c27-9293-cabed0ffe135)


```
2024/02/25 09:14:11 >  [!] lacy@jab.htb - failed to communicate with KDC. Attempts made with UDP (error sending to a KDC: error sneding to jab.htb:88: sending over UDP failed to 10.10.11.4:88: read udp 10.10.14.160:40647->10.10.11.4:88: i/o timeout) and then TCP (error in getting a TCP connection to any of the KDCs)
2024/02/25 09:14:11 >  [!] lalalala@jab.htb - failed to communicate with KDC. Attempts made with UDP (error sending to a KDC: error sneding to jab.htb:88: sending over UDP failed to 10.10.11.4:88: read udp 10.10.14.160:54510->10.10.11.4:88: i/o timeout) and then TCP (error in getting a TCP connection to any of the KDCs)
2024/02/25 09:14:12 >  Done! Tested 13254 usernames (103 valid) in 504.201 seconds
```
