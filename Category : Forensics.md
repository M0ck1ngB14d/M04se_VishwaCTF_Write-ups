# Category : Forensics
# CHALLENGE 1: Smoke out the rat
#### DESCRIPTION
*There was a major heist at the local bank. Initial findings suggest that an intruder from within the bank, specifically someone from the bank's database maintenance team, aided in the robbery. This traitor granted access to an outsider, who orchestrated the generation of fake transactions and the depletion of our valuable customers' accounts. We have the phone number, '789-012-3456', from which the login was detected, which manipulated the bank's employee data. Additionally, it's noteworthy that this intruder attempted to add gibberish to the binlog and ultimately dropped the entire database at the end of the heist.*

*Your task is to identify the first name of the traitor, the last name of the outsider, and the time at which the outsider was added to the database.*

*Flag format : VishwaCTF{TraitorFirstName_OutsiderLastName_HH:MM:SS}*
#### SOLVING
First I read the description many times to try to collect hints. Then I opened the database binary log file given: **DBlog-bin.000007** using
```mysqlbinlog DBlog-bin.000007```

I could not get much since I tried to search for this number 789-012-3456 and got "not found". That was because the Binlog was non-human readable. After some research I found out that addding ```-v``` DBlog-bin.000007 is opened with non-human and human readable versions of Binlog. (adding ```-vv``` you get also the metadata of each statement). 

```mysqlbinlog DBlog-bin.000007 -v > DBlog-bin.txt```

Now I searched in DBlog-bin.txt for the number 789-012-3456 and found out it is of Matthew, a database administrator and is on the table of bank maintainers. 

![Screenshot from 2024-03-05 23-54-42](https://hackmd.io/_uploads/rJY64GH6T.png)


This is the traitor's first name. First part done !

To get the outsider's name, I needed to look into the employees table because of this sentence in the description: *"which manipulated the bank's employee data*". Basically Matthew added the outsider to the employee table to give him access to the database.

I knew this querry will not be with the other ones relevant to adding employees. And I was right. Keep following the file I found out the statement:

![Screenshot from 2024-03-06 00-01-54](https://hackmd.io/_uploads/rkaFUMBpa.png)

It was not a INSERT querry but an UPDATE one. duuh!

I got sure when I saw the updates querries made to accounts balances after this update:

![Screenshot from 2024-03-06 00-06-51](https://hackmd.io/_uploads/B1LeOzBap.png)

Darwin is the outsider's last name. Second part done !

The time was firstly a struggle because as it is shown the time when Darwin was added is 11:01:29, but once I tried the flag it got rejected. The problem was due to different timezones; there is a difference of 4,5 hours between my timezone and IST.

The actual time was 15:31:29. Third part done !
# CHALLENGE 2: Router |port|
#### DESCRIPTION
*There's some unusual traffic on the daytime port, but it isn't related to date or time requests. Analyze the packet capture to retrieve the flag*
#### SOLVING
I started by searching on the internet for daytime port wireshark and found out that daytime is actually a tcp/upd protocol name so I tried to filter on it on the Capture.pcapng file they gave us and it worked.

![Screenshot from 2024-03-06 00-41-13](https://hackmd.io/_uploads/rk2ylmra6.png)

In each of these packets there is a segment called "Daytime Protocol" and its parameter "Daytime" contain a text that is encrypted. 

![Screenshot from 2024-03-06 01-06-33](https://hackmd.io/_uploads/SJ2cSXH6p.png)


First I extracted all the lines from Capture.pcapng that contained Daytime using ```tshark -r Capture.pcapng -V | grep "Daytime"``` and got this:

![Screenshot from 2024-03-06 01-07-29](https://hackmd.io/_uploads/BJz0H7B6p.png)

Then using a python script I got rid of what is not part of the text:

![Screenshot from 2024-03-06 01-09-03](https://hackmd.io/_uploads/HJg48XrTa.png)

Now that I have the whole text I need to decrypt it. I used dcode identifier (https://www.dcode.fr/cipher-identifier) to try and identify the encryption method. It was ROT13.

Here is the actual message:

*Hey, mate! 
Yo, long time no see! You sure this mode of communication is still safe?
Yeah, unless someone else is capturing network packets on the same network we're using. Anyhow, our text is encrypted, and it would be difficult to interpret. 
So let's hope no one else is capturing. 
What's so confidential that you're about to share? 
It's about cracking the password of a person with the username 'Anonymous.' 
Oh wait! Don't you know I'm not so good at password cracking? 
Yeah, I know, but it's not about cracking. It's about the analysis of packets. I've completed most of the job, even figured out a way to get the session key to decrypt and decompress the packets. 
Holy cow! How in the world did you manage to get this key from his device? 
Firstly, I hacked the router of our institute and closely monitored the traffic, waiting for 'Anonymous' to download some software that requires admin privilege to install. Once he started the download, I, with complete control of the router, replaced the incoming packets with the ones I created containing malicious scripts, and thus gained a backdoor access to his device. The further job was a piece of cake. 
Whoa! It's so surprising to see how much you know about networking or hacking, to be specific. 
Yeah, I did a lot of research on that. Now, should we focus on the purpose of this meet? 
Yes, of course. So, what should I do for you? 
Have you started the packet capture as I told you earlier? 
Yes, I did. 
Great! I will be sending his SSL key, so find the password of 'Anonymous.' 
Yes, I would, but I need some details like where to start. 
The only details I have are he uses the same password for every website, and he just went on to register for a CTF event. 
Okay, I will search for it. 
Wait a second, I won't be sending the SSL key on this Daytime Protocol port; we need to keep this untraceable. I will be sending it through FTP. Since the file is too large, I will be sending it in two parts. Please remember to merge them before using it. Additionally, some changes may be made to it during transfer due to the method I'm using. Ensure that you handle these issues. 
Okay! ...*

Upon reading the text I knew that the next step is to go search for the SSL key split on 2 FTP protocol packets.

I didn't find FTP packets but found FTP-DATA packets and there were 2 of them. The ones we look for.

![Screenshot from 2024-03-06 01-24-20](https://hackmd.io/_uploads/HJvatmrap.png)

These two packets had the SSL keys on the "Line-based text data servers", so after extracting them and also identifying the encryption method which was ROT20 and decrypting them, I put them on a SSL-keys.log file.

Then imported it to wireshark so it can be able to decrypt SSL, I did that by going to ```Edit -> Prefrences -> Protocols -> TLS``` and then putting the file on "(Pre)-Master-Secret log filename" input:

![Screenshot from 2024-03-06 01-42-12](https://hackmd.io/_uploads/BJf6R7Spp.png)

Then after decryption and after searching for the string "password" I found the password which was actually the flag.

![Screenshot from 2024-03-06 04-08-09](https://hackmd.io/_uploads/BJo4xIBaa.png)


# CHALLENGE 3:Repo Riddles
#### DESCRIPTION
*We got a suspicious Linkedin post which got a description and also a zip file with it. It is suspected that a message is hidden in there. Can you find it?*

*LinkedIn Post Description:*

*Title: My First Project -The Journey Begins*

Hello fellow developers and curious minds!

*I'm thrilled to share with you my very first Git project - a labor of love, dedication, and countless late-night commits. 🚀*

*Explore the code, unearth its nuances, and let me know your thoughts. Your feedback is invaluable and will contribute to the ongoing evolution of this project.*
#### SOLVING
First I extracted the github repository given "My First Git Project.zip" and searched for hidden files using ```ls -a``` and found out .git in LearningGit folder. And then using GitTools I was able to extract from it the commits history and its content.

![Screenshot from 2024-03-06 03-12-28](https://hackmd.io/_uploads/HyWNmHBpp.png)


After I started reading the files I found out that the flag is split between muliple files and in each of them it was in this format: string[] = "", so all I needed to do is to search for "string".

![Screenshot from 2024-03-06 03-18-26](https://hackmd.io/_uploads/B1JoNSSTa.png)


I got almost all the flag except the charactets having indexes of 6,7 and 8. So I kept looking in the folders and I found an image containing the missing part of the flag.

![309551395-a8dce862-15d5-42a2-a5c8-b672daf04266](https://hackmd.io/_uploads/H1bQDVra6.png)





