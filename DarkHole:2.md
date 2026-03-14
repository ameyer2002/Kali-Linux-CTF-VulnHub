# DarkHole2

As always, starting my lab with a netdiscover command to see what machines are currently running in my network. I know the **192.168.19.129** IP is the victim machine since the other IPs are my machines that are up and running in a homelab.

screenshot 1

I then run a nmap scan to see what ports are open: **nmap -sS -sC --script vuln 192.168.19.129**. Ports 22 and 80 are open. What's interesting is that there is an exposed route **/.git/** under port 80.
