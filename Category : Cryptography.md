# Category : Cryptography

## CHALLENGE 1 : Lets smother the king
#### DESCRIPTION
*In my friend circle, Mr. Olmstead and Mr. Ben always communicate with each other through a secret code language that they created, which we never understand. Here is one of the messages Mr. Ben sent to Mr. Olmstead, which I somehow managed to hack and extract it from Ben's PC. However, it's encrypted, and I don't comprehend their programming language. Besides being proficient programmers, they are also professional chess players. It appears that this is a forced mate in a 4-move chess puzzle, but the information needs to be decrypted to solve it. Help me out here to solve the chess puzzle and get the flag.*

*Flag format: VishwaCTF{move1ofWhite_move1ofBlack_move2ofWhite_move2ofBlack_move3ofWhite_move3ofBlack_move4ofWhite}.*

*Note: Please use proper chess notations while writing any move.*
#### SOLVING
I first started trying to decrypt code.txt, but did not get any result. Then I came back to understand more the description and started searching "Mr. Olmstead and Mr. Ben", also did not get much to work with. Then I searched "Mr. Olmstead and Mr. Ben programming language" I got an article talking about the hardest programming language ever invented by Ben Olmstead. It is called **Malbolge**.

I interpreted code.txt using: https://malbolge.doleczek.pl/

I got: **White- Ke1,Qe5,Rc1,Rh1,Ne6,a2,b3,d2,f2,h2 Black- Ka8,Qh3,Rg8,Rh8,Bg7,a7,b7,e4,g2,g6,h7**

This is a chess puzzle position, and as the description says it is a chess puzzle for a white 4-move forced mate. 

I used a chess tool that let me set the pieces to be as this chess puzzle position and followed its analysis for a white 4-move forced mate. Bingo !!!

----
----
## CHALLENGE 2 : Teyvat Tales

*Description : All tavern owners in Mondstadt are really worried because of the frequent thefts in the Dawn Winery cellars. The Adventurers’ Guild has decided to secure the cellar door passwords using a special cipher device. But the cipher device itself requires various specifications….which the guild decided to find out by touring the entire Teyvat.*


The first you see on the website is this : 

![teyvat1](https://hackmd.io/_uploads/SyqvReSTa.png)

so i guess we need to enter some ciphers to the site will perform maybe something after analyzing the source code i found this in `script.js`

![teyvet2](https://hackmd.io/_uploads/S160CgSpT.png)


So if we look closely at the file we will find the keys for each input and they are : 

`enigma m3`
`ukw c`
`rotor1 i p m rotor2 iv a o rotor3 vi i n`
`vi sh wa ct fx`

when i enterd those keys i got this page that displays a encrypted text


![teyvet3](https://hackmd.io/_uploads/rJnY1bra6.png)



so after carefull analyzing on what is the encryption i found out that it has to do with the enigma machine thats why in the keys there is for example some configuration of rotors etc... then i stumbled on this website that simulates the enigma machine and i used it to decrypt the cipher.

![teyvat4](https://hackmd.io/_uploads/Hkgk-bBa6.png)

flag : `VishwaCTF{beware_of_tone_deaf_bard}`

-----
-----
## CHALLENGE 3 :Poly Fun 


I was given 3 files : 

    encoded_key.txt : we need to decode it to retrieve the key
    encoded_flag.txt : we need the key to retrieve the flag

    challenge.py which contained the algorithm used to generate the encoded key 

I need to reverse the algorithm of challenge.py. 

```
 def encrypt(p,key):
    return ''.join(chr(p(transform(i))) for i in key)
key = open('key.txt', 'rb').read()
enc = encrypt(poly,key)

 ```

'encrypt' is the function that encrypts the key , let's reverse it .

for each i in key (key.txt is opened in binary mode) the function applies : transform() -> p() -> chr() and concatenates the results using join(). 

let's reverse it back : 

1- i use a script that takes each character in encoded_key.txt and reverse it from ascii to decimal and put it in a list.

    ```
    def ascii_file_to_decimal_list(file_path):
    decimal_list = []
    with open(file_path, 'r') as file:
    ascii_code = file.read()
    for char in ascii_code:
        # Convert each character to its decimal representation
        decimal_value = ord(char)
        # Append the decimal value to the list
        decimal_list.append(decimal_value)
    return decimal_list

    file_path = 'encoded_key.txt' 
    result = ascii_file_to_decimal_list(file_path)
    print("Decimal List:", result)

    ```

this is the result 

```

    Decimal List: [9758, 10157, 10564, 10979, 11402, 11833, 12272, 12719, 13174, 9758, 9367, 9758, 9758, 9758, 10157, 9758, 10564, 9758, 10979, 9758, 11402, 9758, 11833, 9758, 12272, 9758, 12719, 9758, 13174, 10157, 9367, 10157]

```

2- now for each number in the list we need to find the positive root of the polynome : 

p(x) = 4x² + 3x + 7 = List[i] 

then we concatenates the end result , the following script solves the equation and concatenates the results we give it as an input the decimal list.

```
    import cmath

def concatenate_numbers(number_list):
    concatenated_string = ''.join(str(num) for num in number_list)
    return concatenated_string

def separate(number):
        digits = [ int(digit) for digit in str(number) ]
        return digits


def solve_equation(x):
    # Define the polynomial coefficients
    coefficients = [4, 3, 7 - x]  # Coefficients for 4a^2 + 3a + 7 - x = 0

    # Calculate the discriminant
    discriminant = coefficients[1]**2 - 4*coefficients[0]*coefficients[2]
    
    # Check if discriminant is non-negative for real roots
    if discriminant >= 0:
        # Calculate real roots
        root1 = (-coefficients[1] + cmath.sqrt(discriminant)) / (2*coefficients[0])
        root2 = (-coefficients[1] - cmath.sqrt(discriminant)) / (2*coefficients[0])
        
        # Return positive root
        if root1.real >= 0:
            return root1.real
        elif root2.real >= 0:
            return root2.real


mylist=[9758, 10157, 10564, 10979, 11402, 11833, 12272, 12719, 13174, 9758, 9367, 9758, 9758, 9758, 10157, 9758, 10564, 9758, 10979, 9758, 11402, 9758, 11833, 9758, 12272, 9758, 12719, 9758, 13174, 10157, 9367, 10157]

results = [solve_equation(x) for x in mylist]
results2 = []
for i in results:
        results2.append(int(i))

print(results2)

print("----------")
print(concatenate_numbers(results2))
```

here is the end result if we run the script :
4950515253545556574948494949504951495249534954495549564957504850


the last thing to do is to reverse the transform() function . In challenge.py the transform(num) always equals to num . 
so now the decoded key is : ```4950515253545556574948494949504951495249534954495549564957504850 ```

but if we try to decrypt the flag using it we won't succed because as i said before the key.txt file was opened in binary mode so we need to transform each decimal value to its corresponding ascii character . 

```
# List of decimal values representing bytes
decimal_values = [49, 50, 51, 52, 53, 54, 55, 56, 57, 49, 48, 49, 49, 49, 50, 49, 51, 49, 52, 49, 53, 49, 54, 49, 55, 49, 56, 49, 57, 50, 48, 50]


# Transform each decimal value to its corresponding ASCII character
ascii_characters = [chr(decimal) for decimal in decimal_values]

# Join the ASCII characters into a string
ascii_string = ''.join(ascii_characters)

print(ascii_string)
```

the result now is 12345678910111213141516171819202

finally we use any online aes decrytion tool , we specify that  the key size in bits are 256 

![screenPolyFun](https://hackmd.io/_uploads/Sy8mxUST6.png)


----
----
## CHALLENGE 4 : Intellectual Heir



Attachments : 'Intellectual Heir.py' , file.txt , file1.txt , file2.txt

the python file is the algorithm used to encrypt the message . it takes a string as an input and then converts it to its equivilent ascii numbers , and performs the following operation : ```pow(msg,e, f)``` ; msg  

msg : the string input converted to numbers
e : the exponent , in cryptography e is usually 3 or 65537 (we'll use the later)

f : the modulus

finally it prints the encrypted message

To decrypt the message we need to find two prime numbers 'a' and 'z' that verifies ```f = a * z```

the python file also contains this code : 

```#bamm!! bamm!! double protection for primes
bin_arr = np.array(list(bin), dtype=int)
result = np.sin(bin_arr)
result = np.cos(bin_arr)
np.savetxt("file1", result)
np.savetxt("file2", result)
```

it transforms a number into its binary equivilent , after that creates a np.array from the binary number and stores the result of np.sin and np.cos into 2 seperate files . 

knowing this, and looking into file 1 and 2,  we can assume that file1.txt and file2.txt are the np.cos and np.sin results respectively.

the following simple scripts can transform the files into their decimal equivilent . 

```
    def convert_value(value):
    if value == "0.000000000000000000e+00\n":
        return '0\n'
    elif value == "8.414709848078965049e-01\n":
        return '1\n'
    else:
        return None

# Input and output file paths
input_file_path = "file2.txt"
output_file_path = "output2.txt"

# Read input file and process each line
with open(input_file_path, 'r') as input_file:
    lines = input_file.readlines()

# Convert each value and write to output file
with open(output_file_path, 'w') as output_file:
    for line in lines:
        converted_value = convert_value(line)
        if converted_value is not None:
            output_file.write(converted_value)

```

for the case of file1.txt , we replace the values in the script with ```1.000000000000000000e+00``` (return 0) and ```5.403023058681397650e-01``` (returns 1 )

the output of this script is a file that has binary numbers , we concatenate it and transform it to its equivilant decimal number .

we then inject the two numbers we got into 'a' and 'z' inside the script ; 

```a=57357445697656305449852658985072306792176526325401427689338172257827853689473430283849367024117704513636066741450894144354439223```

```z=89992838080292432041749786501934273286234288253944531238372481458518903256335509625431026718322552331965908097158513049639942869 ```

a simple online prime number checker assures us that a and z are in fact prime numbers .

finally here's the final python script : 


```
    # my secret to hide the combination of my safe in fornt of all without anyone getting a clue what it is ;)

#some boring python function for conversion nothing new
def str_to_ass(input_string):
    ass_values = []
    for char in input_string:
        ass_values.append(str(ord(char)))
    ass_str = ''.join(ass_values)
    return ass_str

input_string = input("Enter the Combination: ")
result = str_to_ass(input_string)
msg = int(result)

#not that easy, you figure out yourself what the freck is a & z
a = 57357445697656305449852658985072306792176526325401427689338172257827853689473430283849367024117704513636066741450894144354439223 
z =89992838080292432041749786501934273286234288253944531238372481458518903256335509625431026718322552331965908097158513049639942869 

f = (a * z) #cant remember what goes in the question mark
e = 65537 # or 3 what is usually used

#ohh yaa!! now you cant figure out $h!t
encrypted = pow(msg, e, f)
print(str(encrypted))

```

To decrypt file.txt , we need the 2 prime numbers that constitutes f ``` f = a * z ``` , and we have them . Now we can calculate the exponent ```d = inverse(e, (p-1)*(q-1))``` required for the decryption following this operation : ```decrypted_msg = pow(encrypted_msg, d, f)```

here is the script used : 

```
    from Crypto.Util.number import inverse

# Encrypted message
encrypted_msg = 4400037514278889258479265625258024039636437755883377709505596356049534358755375772484057042989024750972247184288820831886430459963472328358741858934783775986591400972020736548834642094922678189447202173710409868474198821576627330424767999152339702779346380

# Public exponent
e = 65537

# Modulus
p =57357445697656305449852658985072306792176526325401427689338172257827853689473430283849367024117704513636066741450894144354439223
q =89992838080292432041749786501934273286234288253944531238372481458518903256335509625431026718322552331965908097158513049639942869
f = p * q

# Private exponent
d = inverse(e, (p-1)*(q-1))

# Decrypt the message
decrypted_msg = pow(encrypted_msg, d, f)

print("Decrypted message:", decrypted_msg)

```

running the script will give us the flag in ascii 
![screenIntellectualHair](https://hackmd.io/_uploads/Hys8lUSp6.png)



transforming it into string gives us the flag ! 

![screenIntellectualHair2](https://hackmd.io/_uploads/rJLDl8HTa.png)

## CHALLENGE 5 : Happy Valentine's

we were given two files : source.txt & enc.txt

```
from PIL import Image
from itertools import cycle

def xor(a, b):
    return [i^j for i, j in zip(a, cycle(b))]

f = open("original.png", "rb").read()
key = [f[0], f[1], f[2], f[3], f[4], f[5], f[6], f[7]]

enc = bytearray(xor(f,key))

open('enc.txt', 'wb').write(enc)
```

from the source.txt we understand that original.png is Xored with a key which is the image's first 8 Bytes which is the header. so key = 0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A

we need now to reverse the xor operation , lukily the reverse of xor is xor itself , so we input the enc.txt .
we use this script to decrypt :

```
from PIL import Image
from itertools import cycle

def xor(a, b):
    return [i ^ j for i, j in zip(a, cycle(b))]

# Read the encrypted file
enc = open('enc.txt', 'rb').read()

# Read the key from the original encrypted image
# key = [enc[0], enc[1], enc[2], enc[3], enc[4], enc[5], enc[6], enc[7]]
key = [ 137, 80, 78, 71, 13, 10, 26, 10]

# Decrypt the image
decrypted_data = bytearray(xor(enc, key))

# Save the decrypted image
with open('decrypted_image.png', 'wb') as f:
    f.write(decrypted_data)
```

we get after that an image with the flag : VishwaCTF{h3ad3r5_f0r_w1nn3r5}