# Hacker kid 1.0.1

As normal, starting off my reconaissance with a netdiscover command to identify the IP associated with the vulnerable host.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/9256b484-8a6d-4655-b49c-db3447a1c95c" />

After identifying the vulnerable host, I can run my nmap scan to see what services and ports are currently open.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/7f44d9ba-1cef-438d-a32a-6a3a0f2aa7ff" />
<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/3ee298f4-b524-4afe-a2ee-2f665c716805" />

I like to inspect the web app as part of my reconaissance to see if any notes are left behind in the HTML that could give us a clue or potentially release sensitive information. When inspecting this page, under the font section I can see a note that says to use the parameter **page_no**. I appended this to the URL and set a value of 1 to see what it would return.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/5ed61b72-0bb4-4512-be23-1f81b26dc72b" />
<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/bb4c3ad4-91c5-4d57-be66-d4a1f5c3f756" />

That parameter feels like it can be exposed through a brute force. To achieve this, I can use Burp to capture a GET request and then use repeater to send in different payloads to the value of the parameter. I'll start with a range of 1-50 and see if any of the payloads used have a noticeably different length that could be used to expose this parameter. I'll place the payload on the value of the parameter and change the payload type to numbers.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/68d13a78-e78e-49b0-af71-95794dd78ced" />

Looking at the payload value of 21, I see the length is much longer than all other payloads used. I'll put 21 in the URL and see what that shows.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/69d25ba0-c3ad-4c1e-8b8b-42ea3aa18fb6" />

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/c8bba3ce-f455-4775-9859-ec6ccbb8d3ff" />

Earlier when I accessed the web app, there was a message on the bottom of the screen that emphasized Dig which I knew had to be a clue to run the dig command eventually. Once I was able to get the subdomain, **hackers.blackhat.local**, to appear, I could run the dig command to check if there were any other subdomains associated with the IP. I ran the command **dig hackers.blackhat.local @192.168.19.134** and found a subdomain called **hackerkid.blackhat.local**.

<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/a3f2021c-215c-46c4-a185-a4497207ebec" />
<img width="1918" height="920" alt="image" src="https://github.com/user-attachments/assets/b45d1aa5-0c34-4f89-bc9d-3d6c985c004c" />
