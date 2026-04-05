# Snakeoil

As always, I like to start reconaissance with a **netdiscover** command to see what addresses are currently running on my network.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/5c92ec52-3531-4920-bcbb-92807638f097" />

After I found the IP associated with the target machine, I then run a nmap scan. I have recently been including **--script vuln** in my scan to show any exposed paths or vulnerabilities associated with each open port on the machine. I ran **nmap -sS -sV --script vuln 192.168.19.131** and the following was returned:

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/0fb81217-2408-4dc3-afc3-ef9b8d948aa2" />

I checked the host on port 80 but there wasn't much there.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/0f43b0bb-6a2d-4e94-a51f-c81f8ca60535" />

I then checked the host on port 8080 and found some more information. On this there were 3 links all to different pages. I also created a new post to see if anything would come from that but no luck. 

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/4611e8aa-ee14-4c61-a830-74803c3e2e03" />
<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/81df6a41-9c90-4ebb-b86f-9d991750e112" />
<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/e19559d0-0ec7-4d57-8223-92895f6d935d" />

There is a link **https://flask-jwt-extended.readthedocs.io/en/stable/options/** that leads to Flask-JWT documentation.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/54bb666c-6ada-4f18-9bfc-dc753baa5f79" />

I ran a gobuster scan to enumerate the directories for this host. I found a few interesting directories.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/f5fab933-ebb9-4379-8ed7-ad21a915e433" />

Looking at the /users directory, I found a username and hashed password to an account but I'm not entirely sure which account these credentials are for quite yet. I tried cracking the password but was unsuccessful.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/47d87f6e-ee1f-42a5-bc99-e835cc98da7b" />

The other directory I explored was /registration which simply had a message regarding using the wrong method.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/08ac9fd0-bd6b-441b-8313-f9af93e52521" />

From here, I opened Burp so I could intercept a request and change the method. I did this and changed the request to a POST method and sent it to the server. I received an interesting return about the username field. Username field cannot be blank so I changed my request to include a username.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/7e050409-3edd-4019-9544-85c4de7a3ddb" />
