# Five86:1

# Enumeration
After installing the Five86:1 machine, your first step in Kali will be to run this command to check what devices are connected to the internal network: **ip a**. Following this, you can take the network interface name and then type in the command: **netdiscover -i eth0**
<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/82346535-ed87-4cf9-a6cc-507cf8009cbf" />

I am now going to run an nmap scan with a bunch of different commands, all of which I gathered from the internet. There are over 200 nmap commands so it's important to do some research to see which command is best for the scan. In a real-world Pen Test, you'll want to run as little scans as possible as this creates logs that will leave footprints.

I ran this command: **nmap -v -T4 -p- -sC -sV -oN nmap.log 192.168.77.129**

What stands out to me is the file **robots.txt** under port 80. It has a path of /ona.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/90168f74-7a78-49c2-a5f0-80b3ed46f3a3" />

I'll then take the IP of the vulnerable machine and paste it into a tab with **/ona** appended to it.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/acf7125b-a7ec-424f-8434-4358d8c62d24" />

# Exploit
Something I have been learning with red-teaming is that you're likely going to have to do research during the lab to find additional information that can be helpful. In the Kali terminal, I ran **searchsploit opennetadmin** and found a vulnerabilities. Also, through a quick search, I found that OpenNetAdmin v18.1.1 is vulnerable to **Remote Code Execution (RCE)**.


<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/cb408d10-0f22-44a7-9be2-c0e9d9f2ce7b" />

I then ran this command to send a malicious HTTP request to the web server which exploits improper input validation. The command output is sent back in the HTTP response and prints to my terminal. 47691 is just an ID from the Exploit-DB: **bash 47691.sh http://192.168.77.129/ona/**


<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/bc26b405-4c43-4797-ae94-edbe6442ef5f" />

I then used the cat /etc/passwd to display the list of user accounts.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/2b6a2749-e25f-433e-bd03-aa067de86344" />

I then looked over all the accounts and noticed for **www-data**, there's a path /var/www so I decided to try looking at the content of that path.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/900ea189-e3bd-4a85-a824-1c478bceb8a1" />

I then went back to my root terminal and ran the command: **crunch 10 10 aefhrt -o passwords.txt**
* **crunch** runs the crunch tool
* **10 10** is the minimum and maximum password length
* **aefhrt** is the character set
* **-o** is for output
* **passwords.txt** saves to this file
This tool will generate every possible 10-character combination using **aefhrt**.

I then used John The Ripper to crack the hash. As you can see, the password is **fatherrrrr**.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/364bc4ca-a1f1-425b-a802-adf535d10e1c" />

I then SSH into Douglas's account with that password.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/ba031e81-f136-4e05-8a95-7ebe01a629a2" />

# Privilege Escalation
I can sudo /bin/cp with no password as the user jen. So, now I just need to find something to copy that is useful. I noticed the user douglas had an id_rsa file in .ssh of the home directory, so I thought the user jen might have one too. I tried going that route but couldn’t copy any files from jen’s .ssh directory because all the filenames I tried were not found. Eventually, I used **find** to search for files owned by jen and I found /var/mail/jen. I created a file in /dev/shm with **touch** and named it test2 and changed the permissions to chmod 777. Then I ran the command: **sudo -u jen /bin/cp /var/mail/jen ./test2**. I then used the **cat** command to read the file.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/d9016500-19dc-4813-8ac4-c33a40fd4e69" />

Roy sent Jen an email with the user Moss’s new password so I can try and go sudo into his account. I logged into Moss's account with the password **Fire!Fire!** and changed the directory so I could list the home directory.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/fab21b9c-f79e-4540-8f1d-698666a9dd84" />

I saw a games directory so I went in there and saw the game **upyourgame** was belonged to Moss's root account so I played the game until I got a root shell.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/67590075-568e-43bb-9c2c-83c1c84f6df4" />

I then changed the directory to the root directory and listed all of the files. I saw the **flag.txt** file which I used the **cat** command to display the file.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/0659f666-f223-4e06-9b11-b6f9b921a0e9" />

Finally, the flag has been captured: **8f3b38dd95eccf600593da4522251746**.



