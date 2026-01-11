# Five86:1

After installing the Five86:1 machine, your first step in Kali will be to run this command to check what devices are connected to the internal network: **ip a**. Following this, you can take the network interface name and then type in the command: **netdiscover -i eth0**
<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/82346535-ed87-4cf9-a6cc-507cf8009cbf" />

I am now going to run an nmap scan with a bunch of different commands, all of which I gathered from the internet. There are over 200 nmap commands so it's important to do some research to see which command is best for the scan. In a real-world Pen Test, you'll want to run as little scans as possible as this creates logs that will leave footprints.

I ran this command: **nmap -v -T4 -p- -sC -sV -oN nmap.log 192.168.77.129**

What stands out to me is the file **robots.txt** under port 80. It has a path of /ona.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/90168f74-7a78-49c2-a5f0-80b3ed46f3a3" />

I'll then take the IP of the vulnerable machine and paste it into a tab with **/ona** appended to it.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/acf7125b-a7ec-424f-8434-4358d8c62d24" />
