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
