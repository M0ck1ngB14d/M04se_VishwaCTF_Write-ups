# Category : REVERSE
## CHALLENGE 1 : Sandese Aate hai
*description : A friend pranked me by encrypting my letter but gave me the program by which he encrypted it. Can you help me decrypt it?*

*Replace one of the s with w at the end after receiving the letter*

In this challenge we have 2 attachments Program and encletter.txt

I think the content of encletter.txt had to be different to what's in the program so we can reverse it and get the real flag but anyways when executing program the flag appears:

![rev1](https://hackmd.io/_uploads/rJldoq4aa.png)

Flag is : VishwaCTF{4nd23y_4nd23y3v1ch_m42k0v}

----------------
----------------

## CHALLENGE 2 : Your Bonus

*Description: I am very kind, and you're my friend too. I was about to share some flags with you, but unfortunately, a ransomware attack occurred on the file containing those flags. All the flags got encrypted by the ransomware. After cross-checking the directories, I found the ransomware file and some other related items.*

*I'm going to share that information with you. However, due to the ransomware, I'm unable to provide you with the flags 😥😥. Now, I need your help to recover those flags. Can you assist me, please? Your cooperation would be highly appreciated, and you will receive a reward for your help.*

*Note : Ransomware are not meant to be executed as it can harm your systems (although this won't)*

we got 3 files ransomeware.exe , README.txt and Your_hacked_data0.txt

While analysing the ransomware.exe i find this in the main function : 
```
  std::__cxx11::basic_string<>::basic_string("Flags.txt",&local_6e);
  std::allocator<char>::~allocator((allocator<char> *)&local_6e);
  std::basic_fstream<>::basic_fstream();
  std::basic_fstream<>::open(local_a0,8);
  local_6d = '9';
  puVar2 = (undefined *)std::map<>::operator[]((map<> *)&_HM,&local_6d);
  *puVar2 = 0x26;
  std::__cxx11::basic_string<>::basic_string();
  local_14 = 0;
  std::basic_fstream<>::open((basic_string *)&assd[abi:cxx11],0x10);
  while( true ) {
    piVar4 = (int *)std::getline<>(local_1a0,local_1b8);
    bVar1 = std::basic_ios::operator.cast.to.bool
                      ((basic_ios *)((int)piVar4 + *(int *)(*piVar4 + -0xc)));
    if (!bVar1) break;
    std::__cxx11::basic_string<>::basic_string(local_1b8);
    std::__cxx11::basic_string<>::basic_string(local_a0);
    local_2fc = &stack0xfffffe28;
    devil_function();
    std::__cxx11::basic_string<>::~basic_string(local_6c);
    std::__cxx11::basic_string<>::length();
    local_2fc = &stack0xfffffe28;
    zarathos(&stack0xfffffe10,(basic_string *)local_2fc);
    std::__cxx11::basic_string<>::basic_string((basic_string *)&stack0xfffffe28);
    local_2fc = (char *)local_14;
    local_24 = Lucifer(local_54,local_14);
    std::__cxx11::basic_string<>::~basic_string(local_54);
    ghost_ridders_wepon();
    local_2fc = (char *)local_14;
    matter_manipulation[abi:cxx11]((basic_string<> *)&stack0xfffffdf8,local_14);
    std::__cxx11::basic_string<>::basic_string((basic_string *)&stack0xfffffdf8);
    Trigon(local_3c);
    std::__cxx11::basic_string<>::~basic_string((basic_string<> *)local_3c);
    local_14 = local_14 + 1;
    std::__cxx11::basic_string<>::~basic_string((basic_string<> *)&stack0xfffffdf8);
    std::__cxx11::basic_string<>::~basic_string((basic_string<> *)&stack0xfffffe10);
    std::__cxx11::basic_string<>::~basic_string((basic_string<> *)&stack0xfffffe28);
  }
  std::basic_fstream<>::close();
  std::basic_fstream<>::close();
  _Filename = (char *)std::__cxx11::basic_string<>::c_str();
  iVar5 = _remove(_Filename);
```

it first opens flag.txt then do some changes on it using: 
*   local_1f = '7';
    *puVar1 = 0x21;
*   local_1e = '3';
    *puVar1 = 0x40
*   local_1d = '8';
    *puVar1 = 0x25
*   local_70 = '5';
    *puVar2 = 0x23;
*   local_29 = '6';
    *puVar1 = 0x28
*   local_1d = '2';
    *puVar1 = 0x24;
*   local_2a = '1';
    *puVar1 = 0x29;
*   local_6d = '9';
    *puVar2 = 0x26;
*   local_e = '4';
    *puVar1 = 0x5e;
*   local_d[0] = '0';
    **puVar1 = 0x2a;
which are these symbols=[ '*', ')', '$', '@', '^', '#', '(', '!', '%', '&']

I used this script to find the solution : 

```
import string

symbols = ['*', ')', '$', '@', '^', '#', '(', '!', '%', '&']

with open('Your_hacked_data0.txt', 'r') as file:
    r = file.readlines()

r = [line.strip() for line in r]
for i, symbol in enumerate(symbols):
    print(i, symbol)

def todecimal(line):
    translator = str.maketrans({symbol: str(i) for i, symbol in enumerate(symbols)})
    return line.translate(translator)

def numberstoascii(number_string):
    for j in range(50):
        p = [int(number_string[i:i+2]) + j for i in range(0, len(number_string), 2)]
        try:
            ascii_string = ''.join(chr(a) for a in p)
            if "VISHWACTF" in ascii_string:
                print(ascii_string)
        except ValueError:
            pass

for line in r:
    line = todecimal(line)
    numberstoascii(line)

```

output : 
```
python3 ma.py
0 *
1 )
2 $
3 @
4 ^
5 #
6 (
7 !
8 %
9 &
DL;W?35_4R3_B3AFUL[:)]]F0QVISHWACTF[R4N5^$FLE<
MW4R35_B3AUTIFUL=?_>[:)]]VISHWACTF[<_4R3_R4N50
```

The flag should be this :
VISHWACTF[<_4R3_R4N50MW4R35_B3AUTIFUL=?_>[:)]]

but it's actualy this one :
VISHWACTF[4R3_R4N50MW4R35_B3AUTIFUL]



