# DarkHole2

As always, starting my lab with a netdiscover command to see what machines are currently running in my network. I know the **192.168.19.129** IP is the victim machine since the other IPs are my machines that are up and running in a homelab.

screenshot 1

I then run a nmap scan to see what ports are open: **nmap -Pn -sS -sC --script vuln 192.168.19.129**. Ports 22 and 80 are open. What's interesting is that there is an exposed route **/.git/** under port 80.

What I did from here was create a python env so I could install **git-dumper** which works similarly to wget but this tool works specifically for github repos. The **pip** command doesn't work in my terminal, hence why I created the env for this. I ran the following commands to create my env, activate it, and install git-dumper:

* python3 -m venv gitdumper-env
* source gitdumper-env/bin/activate
* pip install git-dumper

From here, I ran **git-dumper http://192.168.19.129/.git dumped_site** which created a folder called dumped_site/ and downloaded the exposed git repo there. To find logs on previous commits done in this repo, I ran **git log** which showed 3 commits. One was indicating that the user added default credentials into a php file. I then used **git show a4d900** which showed me the changes the user made in this commit. I can see the email used for the dark hole website login is **lush@admin.com** and the password is **321**. From here, I can go back to the website and login with these credentials.

