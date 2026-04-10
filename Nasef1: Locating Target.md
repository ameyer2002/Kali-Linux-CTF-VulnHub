# Nasef1: Locating Target

As usual, starting my lab with a **netdiscover** command to see which IP is the vulnerable machine. Once I run this command, I can run my nmap scan to see what ports are currently open.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/96280f6a-43a1-4530-b0ec-2f05e63de425" />

I will then go to the web app on port 80.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/8a612198-04f3-4a45-a287-b1e7bf3d3b33" />

I've recently been big on brute-forcing directories to find hidden directories and files. I ran dirb and didn't find much so I ran a much different command, fuff. This sends a large number of HTTP requests with different inputs to see what the server responds to which can help me find hidden directories, files, endpoints, etc. After running **ffuf -c -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://192.168.19.133/FUZZ -e .php,.html,.txt, -of html -o dir.html**, I found a file called **goodmath.txt**.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/991a36a7-6028-42cf-bcc7-fa862d7bc53a" />

I checked to see what this file was in the web app and this is what I found, a ssh key.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/53872849-fc27-47e5-8444-5f2cc79c1721" />

After finding the key, I had to download the file so I could change the txt to an id_rsa file. I ran **wget http://192.168.19.133/goodmath.txt** and downloaded it in the **/usr/share/john** directory which I will show you why I did that.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/a5ce60e9-d034-4eae-9890-18753ac145aa" />

Following the download of that file, I changed it to id_rsa which is the default filename for a private SSH key that uses the RSA algorithm. I ran the command **mv goodmath.txt id_rsa**. The reason why this is in the directory I mentioned above is because I am using the ssh2john.py file which lives in the directory and I didn't want to move around files so I just downloaded it so all files were in the same directory. I ran the command **python3 ssh2john.py id_rsa | tee hash** which takes my SSH private key file (id_rsa), extracts its encrypted password data (if it has one), and converts it into a hash format that the john the ripper tool can understand. The pipe takes the output of the first command and sends it into the next command and tee hash saves the output to a file named hash.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/c39dc9d9-3cf5-429c-8650-1dd70d026a26" />

I can now crack the hash using the command **john hash --wordlist=/usr/share/john/rockyou.txt**.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/605fe0a4-e21d-412a-b2c4-3b9f05c4f0bd" />

Awesome. I got the key **reading** which I can use to ssh into agentr's account. I tried doing a normal **ssh agentr@192.168.19.133** command but the password was not being read correctly. I instead ran **ssh -i id_rsa agentr@192.168.19.133** which tells the ssh to use the specific key from my id_rsa file and this worked. I am now in agentr's account.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/67800c3a-6ce5-48ce-9ccf-1c8b90a8de70" />

The first user flag was found in the user.txt file. Now it's time to perform privilege escalation.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/d9ca165c-d0bb-45b2-8945-0b8eae8d53e7" />

From here, I wanted to check the **/etc/passwd** directory to look for passwords associated with different accounts. All accounts have a 'x' in their account which means their account password is hashed in a file in the **/etc/shadow** directory. If I remove the 'x', I no longer need a password to log into an account. I ran **nano /etc/passwd** and removed the x from the root account. I saved this and was able to sudo into the root user account without a password. I then changed the directory to root and read the root.txt file in there to finish the lab.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/db633179-0db2-4579-81d4-fc821b4ce425" />
