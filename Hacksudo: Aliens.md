# Hacksudo: Alien

As always, starting the lab with a **netdiscover** command to identify the IP associated with the target machine. Once I've found it, I will run a nmap scan to see what ports are currently open.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/7a478dc5-d47c-4121-abe7-e11194c41e3e" />

I can then open Firefox to see what the web app looks like before inspecting and snooping around the app.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/d590db39-e2bd-4bd6-ab7c-2f59ba37fd27" />

Nothing stood out like crazy so I ran a gobuster command to see if there were any hidden directories that were exposed to the public. After running **gobuster dir -u http://192.168.19.146 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt,zip~**, I found two directories. One called /images and one called /backup.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/203ee689-0529-47f4-90f1-cbd8273b3df4" />

When appending /backup to the hostname in Firefox, a file appears that looks like it has to do with the target machine's DB. I download the file and run **cat mysql.bak** to read it.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/d51478b3-91ff-4be5-a2b1-bcbd98fcd359" />

Pretty quickly I now have DB credentials. Username is **vishal** and password is **hacksudo**. I'm sure this will be of use later but for now, I will run another gobuster command but for port 9000 since it is running.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/d65b3be9-c6ec-48ad-83c6-9612d022fa82" />

I dug around and got to the /templates directory. I then selected 
