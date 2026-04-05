# Snakeoil

As always, I like to start reconaissance with a **netdiscover** command to see what addresses are currently running on my network.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/5c92ec52-3531-4920-bcbb-92807638f097" />

After I found the IP associated with the target machine, I then run a nmap scan. I have recently been including **--script vuln** in my scan to show any exposed paths or vulnerabilities associated with each open port on the machine. I ran **nmap -sS -sV --script vuln 192.168.19.131** and the following was returned:

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/0fb81217-2408-4dc3-afc3-ef9b8d948aa2" />
