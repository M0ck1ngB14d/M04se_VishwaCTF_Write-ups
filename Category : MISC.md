# Category : MISC
## CHALLENGE 1 : HARDWARE HEIST
*Description : Your cyber gang has stolen a hardware device code from the CIA but are unable to use it. Although while stealing the code, you have found some binary strings which might be helpful to understand the device. You might even find the important codes after solving the device and strings.*

the challenge contains 2 files BINARY INPUT.txt and Hardware.rar

in the hardware that is a lot of files but one of them seems to be the binary that will gives the flag wich is ctfdecoder.vhd:

```
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.STD_LOGIC_ARITH.ALL;
use IEEE.STD_LOGIC_UNSIGNED.ALL;



entity ctfdecoder is
    Port ( Q : in  STD_LOGIC_VECTOR (6 downto 0);
           two : out  STD_LOGIC;
           three : out  STD_LOGIC;
           four : out  STD_LOGIC;
           five : out  STD_LOGIC;
           seven : out  STD_LOGIC;
           underscore : out  STD_LOGIC;
           c : out  STD_LOGIC;
           d : out  STD_LOGIC;
           h : out  STD_LOGIC;
           k : out  STD_LOGIC;
           u : out  STD_LOGIC;
           w : out  STD_LOGIC);
end ctfdecoder;

architecture Dataflow of ctfdecoder is

begin
two <= ((not Q(6)) and Q(5) and Q(4) and (not Q(3)) and (not Q(2)) and Q(1) and (not Q(0)));
three <= ((not Q(6)) and Q(5) and Q(4) and (not Q(3)) and (not Q(2)) and Q(1) and Q(0));
four <= ((not Q(6)) and Q(5) and Q(4) and (not Q(3)) and Q(2) and (not Q(1)) and (not Q(0)));
five <= ((not Q(6)) and Q(5) and Q(4) and (not Q(3)) and Q(2) and (not Q(1)) and Q(0));
seven <= ((not Q(6)) and Q(5) and Q(4) and (not Q(3)) and Q(2) and Q(1) and Q(0));
underscore <= (Q(6) and (not Q(5)) and Q(4) and Q(3) and Q(2) and Q(1) and Q(0));
c <= (Q(6) and Q(5) and (not Q(4)) and (not Q(3)) and (not Q(2)) and Q(1) and Q(0));
d <= (Q(6) and Q(5) and (not Q(4)) and (not Q(3)) and Q(2) and (not Q(1)) and (not Q(0)));
h <= (Q(6) and Q(5) and (not Q(4)) and Q(3) and (not Q(2)) and (not Q(1)) and (not Q(0)));
k <= (Q(6) and Q(5) and (not Q(4)) and Q(3) and (not Q(2)) and Q(1) and Q(0));
u <= (Q(6) and Q(5) and Q(4) and (not Q(3)) and Q(2) and (not Q(1)) and Q(0));
w <= (Q(6) and Q(5) and Q(4) and (not Q(3)) and Q(2) and Q(1) and Q(0));

end Dataflow;
```

i wrote a Python script that takes 7 bits from the input string at a time and decodes them according to the ctfdecoder logic:

```
def ctfdecoder(Q):
    two = (1 != Q[0]) and (Q[1] == 1) and (Q[2] == 1) and (1 != Q[3]) and (1 != Q[4]) and (Q[5] == 1) and (1 != Q[6])
    three = (1 != Q[0]) and (Q[1] == 1) and (Q[2] == 1) and (1 != Q[3]) and (1 != Q[4]) and (Q[5] == 1) and (Q[6] == 1)
    four = (1 != Q[0]) and (Q[1] == 1) and (Q[2] == 1) and (1 != Q[3]) and (Q[4] == 1) and (1 != Q[5]) and (1 != Q[6])
    five = (1 != Q[0]) and (Q[1] == 1) and (Q[2] == 1) and (1 != Q[3]) and (Q[4] == 1) and (1 != Q[5]) and (Q[6] == 1)
    seven = (1 != Q[0]) and (Q[1] == 1) and (Q[2] == 1) and (1 != Q[3]) and (Q[4] == 1) and (Q[5] == 1) and (Q[6] == 1)
    underscore = (Q[0] == 1) and (1 != Q[1]) and (Q[2] == 1) and (Q[3] == 1) and (Q[4] == 1) and (Q[5] == 1) and (Q[6] == 1)
    c = (Q[0] == 1) and (Q[1] == 1) and (1 != Q[2]) and (1 != Q[3]) and (1 != Q[4]) and (Q[5] == 1) and (Q[6] == 1)
    d = (Q[0] == 1) and (Q[1] == 1) and (1 != Q[2]) and (1 != Q[3]) and (Q[4] == 1) and (1 != Q[5]) and (1 != Q[6])
    h = (Q[0] == 1) and (Q[1] == 1) and (1 != Q[2]) and (Q[3] == 1) and (1 != Q[4]) and (1 != Q[5]) and (1 != Q[6])
    k = (Q[0] == 1) and (Q[1] == 1) and (1 != Q[2]) and (Q[3] == 1) and (1 != Q[4]) and (Q[5] == 1) and (Q[6] == 1)
    u = (Q[0] == 1) and (Q[1] == 1) and (Q[2] == 1) and (1 != Q[3]) and (Q[4] == 1) and (1 != Q[5]) and (Q[6] == 1)
    w = (Q[0] == 1) and (Q[1] == 1) and (Q[2] == 1) and (1 != Q[3]) and (Q[4] == 1) and (Q[5] == 1) and (Q[6] == 1)

    
    # Return the corresponding character based on the decoded values
    if two:
        return '2'
    elif three:
        return '3'
    elif four:
        return '4'
    elif five:
        return '5'
    elif seven:
        return '7'
    elif underscore:
        return '_'
    elif c:
        return 'c'
    elif d:
        return 'd'
    elif h:
        return 'h'
    elif k:
        return 'k'
    elif u:
        return 'u'
    elif w:
        return 'w'
    else:
        return None

# Example input
input_str = "110100001101001100011110101101100110110010011010110111110110101111010111000111101011101111101101000110111101111111010000110100011001011001001110111011010001100100110011"

# Iterate over the input string in 7-bit chunks and decode them
decoded_chars = []
for i in range(0, len(input_str), 7):
    chunk = input_str[i:i+7]
    decoded_char = ctfdecoder(list(map(int, chunk)))
    if decoded_char:
        decoded_chars.append(decoded_char)

# Print the decoded characters
print("".join(decoded_chars))

```

this what we got as output : 

```
└─$ python3 decode1.py
h4ck325_5uck_47_h42dw423
```
Flag is : VishwaCTF{h4ck325_5uck_47_h42dw423}

## CHALLENGE 2 : Who am i ?

*Description:*

*Find me on the discord to get your flag
Author : Aditya Gaikwad*

let's search for the author on discord , we find this user in the server : 

![bb](https://hackmd.io/_uploads/rksXmLSaa.png)

Flas is VishwaCTF{1_4m_n0t_4n0nym0u5_4nym0r3}

