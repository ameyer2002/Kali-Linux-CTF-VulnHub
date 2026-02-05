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
