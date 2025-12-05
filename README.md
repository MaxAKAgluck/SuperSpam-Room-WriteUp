# SuperSpam-Room-Writeup
Hack back into the Linux server

Starting with a usual nmap scan:

<img width="1418" height="726" alt="Screenshot 2025-12-05 102426" src="https://github.com/user-attachments/assets/2a197db7-1e59-4fe9-96b6-954942fc67a3" />

I then went to see the webpage:

<img width="764" height="544" alt="Screenshot 2025-12-05 103340" src="https://github.com/user-attachments/assets/a831475f-0708-4801-9ab2-af12499c52ad" />

And the answer to the first question is revealed (yes, if you ran the nmap with -sC you would've seen the CMS version right away in scan results).

I then researched for this version and saw the kind of CVE that's useful to us:

<img width="1242" height="297" alt="image" src="https://github.com/user-attachments/assets/fe0161e5-dae1-4d9c-9b96-1f18b4fd532d" />

But this means we need to log into the CMS admin panel (http://[machine-ip]/concrete5/index.php/login - this is found by examining the main page, there is a Log in button in footer of site).

I then got stuck and didn't understand how to proceed - we have a couple of VNC ports and a webserver wuthout any hints (I only found a couple of possible usernames - http://[machine-ip]/concrete5/index.php/blog)

I decided to scan using nikto - no results, and then used nmap to scan once again just in case (using -p-):

<img width="540" height="231" alt="image" src="https://github.com/user-attachments/assets/bc139166-fc40-48dd-83cb-26e197835ecc" />

And yes we did uncover more ports, upon closer look:

<img width="1263" height="814" alt="image" src="https://github.com/user-attachments/assets/bfbea7ff-2663-4ea3-a7e1-3e29ca823f57" />

SSH and anon login FTP - looks like just what we need to uncover new pieces of puzzle ;)

<img width="1147" height="323" alt="image" src="https://github.com/user-attachments/assets/2664700d-1f6e-4d5b-96e3-51b78b7e411d" />

I downloaded all files from .cap folder (since it was hidden and suspicious) and the note. The IDS_logs folder contained lots of files seemingly encrypted by Evil Spam so I left them for now.

<img width="1247" height="725" alt="image" src="https://github.com/user-attachments/assets/2890f807-2aa9-4dde-8279-8876b2a57c5f" />

Great, we have a hint from Spam himself which means there is something in .cap file (possibly password) which we need to extract.

I googled on how to extract a password from .cap file and then used aircrack-ng:

<img width="1246" height="404" alt="image" src="https://github.com/user-attachments/assets/6bf239dc-1cc4-4600-abe5-f650789fe7de" />

Now we can proceed and log into the CMS by trying the usernames we found:

<img width="335" height="860" alt="image" src="https://github.com/user-attachments/assets/754b7157-354f-42c4-87d7-b8405edfc936" />

I don't know why but the page lagged really hard and got stuck after I clicked "log in" but after reloading (ctrl+f5) we now have access to CMS, which means where must be a place to upload a php shell (like the CVE told us).

I went to system and settings and saw the option for allowed file types so I modified it to include php. Next, go to Files and click on Upload File:

<img width="559" height="774" alt="image" src="https://github.com/user-attachments/assets/cad1a860-39a0-4ad5-b7c6-1133bc33e79a" />

I used the default reverse shell (https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php) and set up a nc listener on 4444:

<img width="853" height="343" alt="image" src="https://github.com/user-attachments/assets/84b74686-66a0-478d-94d3-f2573af4d2c1" />

<img width="1262" height="321" alt="image" src="https://github.com/user-attachments/assets/6ba3deec-e46b-49d4-814b-6a584d5eff7b" />

We are www-data, great :), I search for the flag and it's in home/personal/Work.

I also found a note from Spam in the same parent folder: ''My next evil plan is to ensure that all linux filesystems are disorganised so that these 
linux users will never find what they are looking for (whatever that is)... That should
stop them from gaining back control!
''.

Nothing of interest in user ubuntu or super-spam (for now) or ssm-user, but I found a hidden folder in lucy_loser home:

<img width="1164" height="636" alt="image" src="https://github.com/user-attachments/assets/7c78c5fb-8d8c-4894-8214-00d19af4b31c" />

Wow, looks suspicious enough to check this further, I downloaded all the files.



