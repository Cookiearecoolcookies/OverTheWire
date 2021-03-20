# OverTheWire Bandit writeup

This Page details step by step on how to capture each flag for over the wire bandit challanges.
Link to over the wire: https://overthewire.org/wargames/bandit/


## LVL 0
Flag: 
bandit0
steps:
* ssh as instructed on the level 0 page
ssh bandit0@bandit.labs.overthewire.org -p 2220

## LVL: 0 -> 1
Flag: 
boJ9jbbUNNfktd78OOpsqOltutMc3MY1

steps:
* print the context of readme, command:
strings readme

## LVL 1 -> 2
Flag: 
CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9

steps:
* print the context of "-,", command:
strings ~/-

## LVL 2 -> 3
Flag: 
UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK

steps: 
* print the content of "spaces in the filename", command:
strings ~/"spaces in the filename"

## LVL 3 -> 4
Flag: 
pIwrPrtPN36QITSp3EQaw936yaFoFgAB

steps:
* The instruction say look inside the ~/inhere directory.command:
cd ~/inhere

* However, once in the directory we need to search for the file. command:
ls -lash

* The above command will revealed a ".hidden" file. The -"lash" flags were
passed used to show all files in the directory in a human readable 
format with the files size.

* Now print the file, command: 
strings ./.hidden
 
## LVL 4 -> 5
Flag: 
koReBOKuIDDepwhWk7jZC0RTdopnAYKh

steps":
* Following the instruction we will first navigate into "inhere", command:
cd ~/inhere

* Now lets see if there is a hidden file like before, command:
ls -lash

* The above command reveals, a bunch of files in the directory, meaning 
the flag is hidden in one of the files. Lets take a brute force approach given 
the small amount of files, command:
find . -exec echo "{}" \; -exec strings {} \;

* The above command, will print the filename and the content of each file. 
We were able to do this with one command because we chained the -exec flag. 

## LVL 5 -> 6
Flag: 
DXjZPULLxYr17uwoI01bNLQbtFemEgo7

steps:
* The intructions help us by giving some very important properties. 
To use these we first need to read the man pages, command:
man find

* From the man page, we find the following flags:
-readable
-size
-executable

* Using these three flags we can construct the below find statement, command:
find ./inhere/ -size 1033c ! -executable -readable

* Now we need to print the output of the file we found. 
Note the important "!" in the above command, it indicates not executable. 
To print the found files, command:
strings `find ./inhere/ -size 1033c ! -executable -readable -print`

* The backticks are special, as they indicate that everything inside them 
will be compilled first before the outside. 

Refrence link for further reading:
https://unix.stackexchange.com/questions/27428/what-does-backquote-backtick-mean-in-commands 

## LVL 6 -> 7
Flag: 
HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs

steps:
* The instrunction provide some helpful file properties. But we first need 
to use the man pages to figure out how to use them, command:
man find

* from the man page, we found the following flags to be useful:
-group
-user
-size

* Lets use these and see if there is anything in our current home directory 
which meet the file properties, command:
find . -user bandit7 -group bandit6 -size 33c

* There appears to be no content, meaning we will need to look across the whole 
file system. i.e from "/". If we run a find at that level we will get cluttered 
with permission denied errors. A simple way to avoid the cluter is to filter it 
OUT using grep. However, there are two more key concepts which need to be 
explained before we use grep. 

-- Firstly, we need to understand command pipelines, which is denoted by "|" in 
the terminal. This command send the output of one command into the input 
of another. Here we can be even a bit more specific, we could send only the 
"error output" of command 1 as the input for command 2. This is done by 
using "|&" instead of the regular "|".

-- Secondly, we will need to take the inverse of the grep. Meaning we need to 
filter out all the lines with "Permission denied"

* Now to use the find command and filter the output, command:
find / -user bandit7 -group bandit6 -size 33c |& grep -v "Permission denied" |& grep -v "No such file or directory"

* The result of the above command points us to look inside the file
"/var/lib/dpkg/info/bandit7.password"

* Now to print the content of the file, command:
strings /var/lib/dpkg/info/bandit7.password

## LVL 7 -> 8
Flag: 
cvX2JJa4CFALtqS87jk27qwqGhBM9plV

steps:
* The instrunctions say to look inside the data.txt, however instead of just
outputing the file to the terminal lets, use the other provided information to 
grep the output to find a line with the word "millionth" in it, command:
strings data.txt | grep millionth

## LVL 8 -> 9
Flag: 
UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR

steps:
* The instructions give a lot of hints, but first lets look inside the file 
using vi just to get an idea of what the content looks like, command:
vi data.txt

* Notice how none of the items are grouped togeather in any sequence, but there 
are duplicated items. So lets fix that sorting the data, command:
strings data.txt | sort -n -r

* The above flags for sort can be found in the man pages. The indicate "-n" a 
numeric grouping of the number. 

* Now we need to filter the output for only the one unique line, command:
man uniq

* From the man pages we find the -u flag will file for only the unique content.

* now putting it all togeather, command:
strings data.txt | sort -n | uniq -u

## LVL 9 -> 10
Flag: 
truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk

steps:
* The instructions say a line containg multiple "=" so lets grep the file, using
the -G flag to pass in a regex, command:
strings data.txt | grep -G "==\+"

* The above output shows several lines but clearly only one is the flag. 

## LVL 10 -> 11
Flag: 
IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR

steps:
* The instruction say the file is in base64 encode. So after reading the man 
pages, we know that we can decode content using the "-d" flag, command:
strings data.txt | base64 -d

## LVL 11 -> 12
Flag: 
5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu

steps:
* The instructions say everything has been translated by 13 positions. This means
a to n, needs to be swapped out with n to z, and vise versa. After reading the 
man page for "tr" we know that the command translates one set into another. So 
lets experement with the below command to test our output, command:

echo abcdefghijklmnopqrstuvwxyz | tr [a-no-z] [n-za-z]

* The objective of the above command is to see if we were able to translate all 
the characters by 13 positions. Notice in the output how "a" was swapped 
wiht "n", and "b" with "o", etc. 

* Now we can chain our tr's to satisify the given instructions. 
using the below, command:
strings data.txt | tr [a-no-z] [n-za-z] | tr [A-NO-Z] [N-ZA-Z]

## LVL 12 -> 13
Flag: 
8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL

steps:
* As per the instructions lets move the file and create a new directory, command:
mkdir /tmp/sheep1/ && cp ~/data.txt /tmp/sheep1/ && cd /tmp/sheep1

* Now before we start, the instructions say the file was compressed and 
converted, meaning we need to understand the current file format. By running the,
 command:
file data.txt 

* The instructions say the file is a hexdump, so lets un-hex the file using xxd.
the command xxd with the -r flag is needed, command:
xxd -r data.txt layer1

* The above command will product a file called layer1. The next
few steps will follow the same logic of, running file, looking at the file type
and then decompressing or converting the file format. So for layer1 we need to
find the file format and then change the extention, command:
file layer1
mv layer1 layer1.gz

* Now we need to decompress the gz file, the man pages of gzip, points us to, 
gunzip. The flags needs to decompress is -d, command:
gunzip -d layer1.gz
mv layer1 layer2

* Now we have file called layer2 which is uncompressed form layer1. Running, file
on layer2, reveals that is compressed with bzip2. The man page of bzip2 points us
to bunzip2, commands:
mv layer2 layer2.bz2
bunzip2 -d layer2.bz2

* Sticking with our convention, lets move the output file called a new layer3.
But before we do that lets check the file extention. Which appears to be a gz, 
command:

mv layer2 layer3.gz

* like before we just decompress the .gz file with the following, command:
gunzip -d layer3.gz

* same pattern, file on the output from above reveals that is a POSIX tar archive.
Meaning the extention is a .tar. The following command will rename the file and 
add the approprate extention, command:

mv layer3 layer4.tar

* The man page of tar details that we need to use --extract and -f flags, command:

tar --extract -f layer4.tar

* The output is data5.bin, from the above command. The file appears to be another
tar comressed file. So lets do the above command again, command:

tar --extract -f data5.bin

* The output of the above command was, data6.bin, which appears to be a bzip2
compressed file. so lets move the file into the approprate extention, command:

mv data6.bin layer6.bz2

* Now simmilar to before lets uncompress the file, command:

bunzip -d layer6.bz2

* The output of the file appears to be a tar compressed file. So lets change the
extention, command:

mv layer6 layer7.tar

* Now lets extract the tar like before, command:

tar --extract -f layer7.tar

* The output appears to be a gzip compressed file. So lets change the extention. 

mv data8.bin layer8.gz

* Now lets uncompress the file, simmilare to before. 

gunzip -d layer8.gz

* The output of this appears to be plane asci file. So lets output the
file content, command:
strings layer8

## LVL 13 -> 14
Flag: 
4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e

steps:

1) The instruction say where the password is, and we have a ssh key file in the
bandit13 home directory. The first step would be to try logging into bandit14
using the provided key, command:

ssh bandit14@localhost -i sshkey.private

2) the above command worked, meaning we are now into the bandit14's user account.
Now the instructions say the password is in /etc/bandit_pass/bandit14, so lets go
strings the file, command:

strings /etc/bandit_pass/bandit14

## LVL 14 -> 15
Flag: 
BfMYroe26WYalil77FoDi9qh59eK5xNr

steps:

1) The instruction says there is a port open on 30000 on the localhost. Lets 
confirm this by running nmap with a port ristriction, command: 

nmap -T4 -p 30000 localhost

2) The above command confirms that there is a open port. So lets connect to it,
 command:

nc localhost 30000

3) The port opens, and if I try typing in any character followed by an enter it
produces a message saying "Wrong! Please enter the correct current password"

4) Now lets try connecting again by this time lets pass in the current password,
 command:

nc localhost 30000

5) It opened the connection and returned the flag if I entered in the current
 password, command:

## LVL 15 -> 16
Flag: 
cluFn7wTiGryunymYOu4RcffSxQluehd

steps:

1) Lets assume the service is up on port 30001 instead of checking for it. 

2) Lets try a basic net connect, and see if the instructions are correct with ssl. 

3) Clearly, the net cat approach didn't work. So lets use openssl with s_client 
to try connection to the port, command:

openssl s_client -connect localhost:30001

4) The above command opened waiting for an input. Now following the instructions. 
We pass in the password to get the flag. 

## LVL 16 -> 17
Flag: 
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----

steps:

1) The instruction say scan the ports between 31000 to 32000 on the localhost
, command:

nmap -A -T4 -p 31000 32000 localhost

2) The result of the above command is that two ports are running ssl:
31518; and
31790.

3) based on the instructions provided we should try port 31790 first, given that
nmap produced an error while trying to authenticate. 

4) Now lets open ssl connect with s_client, and enter in the current password,
, command:

openssl s_client -connect localhost:31790

5) The above command, with the current password produced the flag. 

## LVL 17 -> 18
Flag: 
kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd

steps:

1) The above challange produced a key file, which will be used here. Make a new
directory, and cd into it, and create a file called "key". Then past the above 
content into that file, command:

mkdir bandit && cd bandit && touch key

2) Once the content is in the file, we need to lock down the permissions. Given 
it is a private key file, we need to change the permission to 600, command: 

chmod 600 key

3) now lets start the challend by ssh into the box, command:

ssh bandit17@bandit.labs.overthewire.org -p 2220 -i key

4) The instruction say to compare the two files in the home directory. This can 
be done using the simple diff command. However lets use some flags to make our 
lifes easier. Like :
-y : flag less the results come side by side. 
--suppress-common-lines : will not print common lines.

diff -y --suppress-common-lines passwords.old passwords.new 
 
5) The above command will produce the flag for this level. 

## LVL 18 -> 19
Flag: 
IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x

steps:

1) The instruction say dont just ssh like normal as you will just be kicked out. 
The objective here is to read the man pages and understand that commands can be
passed in at the end of the ssh command. These commands will be executed on the
remote host, before exiting, and wont even trigger the .bashrc, command:

ssh bandit18@bandit.labs.overthewire.org -p 2220 "cat readme"

## LVL 19 -> 20
Flag: 
GbKksEFF4yrVs6il55v6gwY5aVje5f0j

steps:

1) Once into the environment, there is a file called "bandit20-do". Once we start
executing we run into some common error messages. like "permisson denied"

2) The clue for this puzzle appears to be in the files name "bandit20-do", this
builds the assumption that we can tell the file to execute bash commands. Given 
the flag is hidden in "/etc/bandit_pass/bandit20". Lets try the bellow command:

/bandit20-do cat /etc/bandit_pass/bandit20

3) This appears to have worked. If it didn't then resorting to analysisng the 
binary trace should be the next logical step. By using commands like ltrace. 
