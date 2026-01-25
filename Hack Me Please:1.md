# Hack Me Please: 1

As always, I will start off the CTF with a **netdiscover** command which will show the IPs of the live decies currently running on the network. I can see the IP **192.168.146.131** is the address for the target machine. I will then run a nmap scan to see what ports are currently open on this machine. I can see port 80 is running a web app which means I can open Firefox and enter the IP address to see what comes up.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/ef544608-5fc6-4169-a3d9-2f5121f44ef4" />

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/785a0755-c839-4fd3-b5fb-a7f6435c4df6" />

Cool, now I like to follow this up with a **searchsploit** command to see if there are any known exploits in the Kali Exploit-DB. I'll run a **searchsploit moonlight** command and I can see there is a privilege escalation exploit available.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/e104a303-ed90-4728-9681-65cd6e758ef3" />

I'll also run gobuster to brute-force the directory which could potentially expose hidden paths that may lead to vulnerabilities. 

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/c6611cf4-046a-4bbb-a32b-f82adfb01196" />

After attempting to access all of the website’s directories, I kept receiving 403 codes as I didn't have access to any of these. My next step was to inspect the main page and look for any clues. I noticed a few different files of code with the path of **.js** which was a path I discovered using gobuster.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/5d014c24-0b14-4fc0-a451-f9b39a1a6f4b" />

I then opened the code for each file until I found something that could be of use. In the **js/main.js**, I noticed there was a path called **/seeddms51x/seeddms-5.1.22/** which was referencing a server endpoint.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/17e1c82a-226a-4a53-8ae5-1a151e8b7803" />

I appended that path to the URL and it took me to a login page for **SeedDMS** where I attempted to login with admin as the User ID and passwd as the password but it failed. This is good though as we now have a username and password prompt to work with.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/a9004d52-7c6e-4f81-af83-051d7651b45c" />

I went back to the terminal and ran **dirb http://192.168.146.131/seeddms51x/** to see if any additional directories would show up which it did. It looks like there are no other additional directories under the **/conf/** directory so I looked this up online to see if there was anymore information on this.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/dc48b5ff-2e2d-4679-9f6f-02f0e8ead5bb" />

After doing some quick research, I found a Github repo with some extensions for the **/seeddms51x/** path. I tried using **http://192.168.146.131/seeddms51x/conf/.htaccess** but received a 403. I then tried **http://192.168.146.131/seeddms51x/conf/settings.xml.template** and was presented with a XML file that conatined the settings for the website. I used **ctrl+f** and searched for password. I found a DB username and password which I then attempted to use to enter a mysql account in the terminal but was getting access denied. So I then tried **http://192.168.146.131/seeddms51x/conf/stopwords.txt** and a wordlist appeared which didn't do much for me. Something I figured would be worth trying was removing the template part of the settings.xml path to see if anything different would appear. The username and password for the DB were different than the template version. The username and password are both **seeddms**.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/85f5132f-d4d3-4609-b9a3-5a6881049407" />

I ran into an issue logging into the mysql account with the normal commands so I just looked up the error message and used this command to login, **mysql -u seeddms -p -h 192.168.146.131 --ssl=0** which worked successfully. 


<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/20bb112c-bf56-47fb-9d11-541d08459ffc" />

After logging into the mysql account I used **SHOW DATABASES;** and found a seeddms DB. I then used **use seeddms;** which put me into that specific DB. I then used **show tables;** which presented all the tables in this DB. I then used **SELECT * FROM users;** and saw an employee name Saket Saurav with their password being **Saket@#$1337**. I attempted to login to the website with these credentials but was unsuccessful. 

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/c08b0374-6721-44f8-8bbf-7ca9dc51b2b1" />

I saw another table called tblUsers so I used **SHOW * FROM tblUsers;**. I found an administrator account with a hashed password. Instead of cracking the hash, I used an MD5 hash generator to create a custom password hash and directly insert it into the database which allowed me to log in as the admin.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/78646b6c-baa4-4441-a3a3-d3f861088768" />

<img width="907" height="610" alt="image" src="https://github.com/user-attachments/assets/beda1eec-c55d-45d9-ac80-e38ec33d1db2" />

I updated the new hash in the DB so I can now successfully login with the username and password as **admin**.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/6ff3e176-663f-405b-9161-30a51a055eec" />

I then found this php reverse shell created by pentestmonkey. I changed the IP address in the script to the target machine's IP and the mysql port as well. AFter doing this, I downloaded the file as a .php file and uploaded it in the document section.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/57557ab6-3ee2-4366-b37a-b52a976b9592" />
