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

I went back to the terminal and ran **dirb http://192.168.146.131/seeddms51x/** to see if any additional directories would show up which it did.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/dc48b5ff-2e2d-4679-9f6f-02f0e8ead5bb" />
