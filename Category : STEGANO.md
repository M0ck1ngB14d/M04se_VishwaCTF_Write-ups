# Category : STEGANO
## CHALLENGE 1 : WE ARE VALORANT 
*Descrption: One day, while playing Valorant, I received a mail from Riot Games that read,*

*“In a world full of light, sometimes the shadows help you win.” “Your Signature move also helps you a lot ; develop one and ace it now.”*

*It also had an image and a video/gif attached to it. I am not able to understand what they want to say. Help me find what the message wants to express.*

In this challenge they gived 2 attachments Astra_!!.mp4 and we_are_valorant.jpg.

When searching the video frame by frame in frame.io in frame 124 : 
![valo](https://hackmd.io/_uploads/HkeEsNtV6T.png)

we can see a word **"Tenz"** and then i find out that's the passphrase for the .jpg file but 1st we have to fix the .jpg with the good magic number using hexeditor : 

```
ff d8 ff e0
```
then using steghide we'll find not_a_secret.txt that contains the flag: 

```
steghide extract -sf we_are_valorant.jpg 
Enter passphrase: 
the file "not_a_secret.txt" does already exist. overwrite ? (y/n) y
wrote extracted data to "not_a_secret.txt".
```
And here we GO : 

```
└─$ cat not_a_secret.txt 
Hello!!
hope you are enjoying the CTF
here's your flag


VishwaCTF{you_are_invited_to_the_biggest_valorant_event}
```
## CHALLENGE 2 : Mysterious Old Case
#### DESCRIPTION
*You as a FBI Agent, are working on a old case involving a ransom of $200,000. After some digging you recovered an audio recording.*

#### SOLVING
We are given a .mp3 file. First I listened to it carefully and I understood that between the music parts there is a voice message that is reversed. So I reversed the mp3 file and used the first website I found that convert mp3 to text (https://www.veed.io/tools/audio-to-text/mp3-to-text).

I got this message:
*I'm John Cooper. It is 24th of November, 1971. Now I have left from Seattle and headed towards Reno. I've got all my demands fulfilled. I have done some changes in the flight log and uploaded it to a remote server. The file is encrypted. The hint for description is the airliner that I was flying in. Most importantly, the secret key is split and hidden in every element of the Fibonacci series starting from two.*

This is very important during next steps.

Now I used exiftool to see the metadata of the file "final.mp3" and found a google drive link and a comment that is a hint for its password.

![Screenshot from 2024-03-06 02-39-43](https://hackmd.io/_uploads/rkLOjVBaT.png)


So obviously when trying to extract the folder I found in the drive "flight_logs" and was asked for a password. I went and searched for a wordlist of lowecase and no spaces words and used launched john the ripper to crack the password (of course after doing zip2john part); but after a thorough reading of the message I undrestood from this part *"he hint for description is the airliner that I was flying in"* that it is unlikely to find D.B. Cooper airliner name on the wordlist so I began to try to find out the password manually; I searched for "DB Cooper flight airliner" and got this:

![Screenshot from 2024-03-06 02-51-45](https://hackmd.io/_uploads/rJ9LRNB6T.png)


I started trying "northwest", "orient", "northwest", "airlines" ... until I tried "northwestorientairlines" and it started extracting.

![Screenshot from 2024-03-06 02-59-41](https://hackmd.io/_uploads/SJx4eHH6T.png)


I got a lot of flight log files and thought the flag will be split between these files following Fibonacci sequence and since there is no file "Flight-2.log" I thought maybe it is Fibonnaci sequence + a random number that I need to find out. I kept looking into the flight log files until I stumbled upon "Flight-305.log", the second I saw it contained the first letters of the flag I got it. DB Cooper flight was the flight 305 and the Fibonacci sequence is actually for the lines numbers.  

![Screenshot from 2024-03-06 03-00-55](https://hackmd.io/_uploads/BJvvgHHaT.png)

## CHALLENGE 3 : Secret Code
Attachments: black jpg file & letter.txt

```
To,
VishwaCTF'24 Participant

I am Akshay, an ex employee at a Tech firm. Over all the years, I have been trading Cypto currencies and made a lot of money doing that. Now I want to withdraw my money, but I'll be charged a huge tax for the transaction in my country.

I got to know that you are a nice person and also your country doesn't charge any tax so I need your help. 

I want you to withdraw the money and hand over to me. But I feel some hackers are spying on my internet activity, so I am sharing this file with you. Get the password and withdraw it before the hackers have the access to my account.

Your friend,
Akshay
```

let's extract hidden data in the jpg file using binwalk -e confidential.jpg :

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
```
0             0x0             JPEG image data, JFIF standard 1.01
116247        0x1C617         Zip archive data, at least v2.0 to extract, compressed size: 72486, uncompressed size: 72530, name: 5ecr3t_c0de.zip
188778        0x2E16A         Zip archive data, at least v2.0 to extract, compressed size: 170, uncompressed size: 263, name: helper.txt
189177        0x2E2F9         End of Zip archive, footer length: 22
```

i found helper.txt and 5ecr3t_c0de.zip

Hey buddy, I'm really sorry if this takes long for you to get the password. But it's a matter of $10,000,000 so I can't risk it out.

"I really can't remember the password for zip. All I can remember is it was a 6 digit number. Hope you can figure it out easily"

the password is 6 digit , it'll be easy to crack it with zip2john and john using a six digits wordlist

5ecr3t_c0de.zip/5ecr3t_c0de.txt:945621:5ecr3t_c0de.txt:5ecr3t_c0de.zip:5ecr3t_c0de.zip
5ecr3t_c0de.zip/info.txt:945621:info.txt:5ecr3t_c0de.zip:5ecr3t_c0de.zip

2 password hashes cracked, 0 left

the zip file contains two txt files .

What are these random numbers? Is it related to the given image? Maybe you should find it out by yourself

```
(443, 1096)
(444, 1096)
(445, 1096)
(3220, 1096)
(3221, 1096)
(38, 1097)
(39, 1097)
(43, 1097)
(80, 1097)
(81, 1097)
(83, 1097)
(93, 1097)
(95, 1097)
...
```

the hint says the numbers are related to the given image (which was the black jpg image) . Maybe they represent pixels , let's generate a script that'll colorize in white each pixel in the file

```
    from PIL import Image

    def read_coordinates(file_path):
        with open(file_path, 'r') as f:
            lines = f.readlines()
        coordinates = []
        for line in lines:
            x, y = line.replace('(', '').replace(')', '').split(',')
            coordinates.append((int(x), int(y)))
        return coordinates

    def draw_white_pixels(image_path, coordinates, output_path):
        img = Image.open(image_path)
        for coord in coordinates:
            img.putpixel(coord, (255, 255, 255)) 
        img.save(output_path)
    image_path = '/confidential.jpg'
    file_path = '/5ecr3t_c0de.txt'
    output_path = 'newImage.png'

    coordinates = read_coordinates(file_path)
    draw_white_pixels(image_path, coordinates, output_path)
```

finally we get this

![final1](https://hackmd.io/_uploads/Hyg8wd8Spa.png)
