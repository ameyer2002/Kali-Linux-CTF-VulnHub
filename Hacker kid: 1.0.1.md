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

From here, I entered information into each of those parameters but everytime I selected register, it said my email could not be used. I used Burp to capture the request to see how it was being sent to the web server and saw it was using XML. I did some digging around and found how to perform a XML injection attack. In my payload of my request, I added **<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>** and used **&xxe;** as the email value while keeping all other parameters empty. Once I sent this request, I was returned the content of the file.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/fddd3a33-5ed1-44a3-8040-d2826226d5d8" />

As per the hacker message, there is something in the home directory of the user. Since I have the /etc/passwd file to see the username, I’s also tried to view some common files from the user directory. 

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/cbc0d693-e1b6-488c-b79e-f2153f21df1c" />

I tried opening the **.bashrc** file in the user directory. The file was in encoded form, so I used base64 in my request to decode it first to check the contents. When looking at the decoded version, I found a username and password for saket's account which are for a python app. When I ran my nmap scan during the reconaissance phase at the start of this lab, an additional port was open 9999. I checked to see if this lead to anything which it prompted me with a username and password.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/05765603-37a6-478a-a715-ddd438cd1ce8" />

When I used admin as the username, it said there was something wrong with my data and to logout despite putting in the correct credentials. Out of curiosity, I tried putting in saket as the username instead which ended up working!

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/4338b5c5-6bbf-4085-b624-6bb3124cf7d1" />

Once I'm logged into saket's account in this app on port 9999, I used Burp to capture a request and then forward it to the server. In the response, I saw the server was a TornadoServer/6.1. I've truthfully never head of this so I wanted to do some research to learn more about it.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/c9caface-7919-45d5-914d-40555130503d" />

<img width="1782" height="936" alt="image" src="https://github.com/user-attachments/assets/28cec771-46ca-4cc7-b5ba-e5b4e021f5ec" />

In the above screenshot, I understand that a GET parameter **name** can pass the values to be reflected in the application response. I tried this in the browser. I entered the name **testing** and it was returned in the application's response. 

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/0b6a9fed-815e-48fa-82a1-b3302fd1547f" />

I can now try a server-side injection by putting a calculation in as a value to the name parameter to see if the application's response is a number.

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/81bb76af-f6a4-4e7c-9560-7e59cb4d8b4d" />

This result shows me that any command can be passed in as a payload and something will be returned.
