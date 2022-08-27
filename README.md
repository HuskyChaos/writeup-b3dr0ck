# TryHackMe - b3dr0ck

![room icon](./room-icon.png)

## Reconnaissance

1. Starting with port scan using rustscan

![nmap-scan-1](./nmap-scan-1.png)

![nmap-scan-2](./nmap-scan-2.png)

We see 4 open ports and a certificate.  
We don't know what to do with the certificate yet so let's just save it in a .cert file for later use.  

2. Let's check the webpage.  

![webpage](./webpage.png)

We are getting redirected to port 4040 and it has a message for us.  
We definitely need that certificate from nmap scan.

3. Little bit more digging.  

Usually my next move is to "curl -v" the page, "searchsploit" the version number, using "feroxbuster"  
and more but it was all just a waste of time except "nc".
I tried using nc to see if i can get something and voilah

![pseudo-shell](./pseudo-shell.png)  
We get some kind of pseudo shell.  
I tried few commands there and heres what i found.  

we can execute very few commands but the most important one was "help".  
"help" gave us another command which requires a certificate and a key.  

![help](./help.png)  

Next i tried cert and key and we got exactly what we needed.  

![cert](./cert.png)  
it's exactly the same certificate we found in nmap scan.  

![key](./key.png)  
With this, we have everything we need for our next step.  


## Foothold

1. Executing the socat command we got from help menu we got another pseudo shell.  
let's see what we get from here.  

2. Type help and we got a password hint.  

![pseudo-2](./pseudoSh-2.png)  

3. I tried to login the ssh port with the aquired information and it worked.
we are in as "######". We can find a flag on home directory.

### Privilege Escalation

1. The first thing i try is "sudo -l". we see that this user can use certutil as super user.  
Lets try using it and see what we can do.

![sudo-l](./sudo-l.png)

2. We can use this to generate certificate and key for a user.  

So here is what i will do.  
1. I read /etc/passwd file and found that there was another user.
2. I used certutil to generate cert,key for that user.
3. used the same socat command with these new cert and key file.
4. We got the same pseudo shell like before and we used help which gave us the ssh password for second user.


### Root

From here on things are easy  
1. I did "sudo -l" and saw something unusual

![sudo-l-2](./sudo-l-2.png)  

2. Going for the first one first.
3. using "sudo base64 filename" will give us a base64 encoded text.
4. Decoding it will give us another base32 encoded text
5. Again decoding it gives us a base64 encoded file.
6. Using hash-identifier for the last one shows that it is a md5 encrypted text.
7. Using crackstation for this one and we got the root password.
8. "su root" enter the password and we have rooted the machine.😁