# The Journey
## Part 1
The Challenge:

![](https://i.imgur.com/f4BS0qT.png)

Firstly from description of the challenge it says we need to convert the hex in `file.txt` to a zip file.<br>
I used python to do that. So I googled it, which let me to this [Stack Overflow thread](https://stackoverflow.com/questions/26796770/writing-hex-data-into-a-file).<br>
**Goal: Use** `binascii` **to convert the hex string to hex number.**

Here is my `asdf.py` python script:
```python
#!/usr/bin/env python3

import binascii

f = open("file.txt", "r")
dataFile = f.read()

dataArr = [x.split(", ") for x in dataFile.split("\n")]
for i in range(0, len(dataArr)-1):
    dataArr[i] = dataArr[i][:-1]

fout = open("hexfile.zip", "wb")
for row in dataArr:
    for col in row:
        fout.write(
            binascii.unhexlify(col[2:])
        )
print("done")
```
Explanation:
- First we open the file and read the content of the file which results in a string.
- We parse this string by splitting by '\\n' and ', ' which will give us an array of rows and columns.
- The rows (except for last one) has empty string at end so remove that.
- We open a new the file that we want to write to.
- We can now loop over the columns and rows of the file and write the hex to the file.
- Note: binascii takes a string '9f' not '0x9f' so we take only the last two chars.

![hexfile.zip is shown in file-explorer](https://i.imgur.com/vAWFOKS.png)

**Goal: Complete.**

## Part 2
We now have a zip file `hexfile.zip` we can use `unzip hexfile.zip` to extract the files in it.<br>
Doing that gives us `encrption.pdf` if we try to open it, we see it needs a password.

![encrption.pdf is shown in file-explorer](https://i.imgur.com/lYi0ITA.png)

**Goal: Crack/Decrypt the PDF's password.**

So I start by googling 'how to decrypt pdf ctf'.<br>
Results:
- [CTFtime.org / EvlzCTF / Yahoo / Writeup](https://ctftime.org/writeup/8707)
- [Cracking Encrypted PDFs – Part 1 | Didier Stevens](https://blog.didierstevens.com/2017/12/26/cracking-encrypted-pdfs-part-1/)

I decided to go to second result to learn more in-depth about cracking PDFs.

I learnt about scripts like pdfid and pdf-parse which are well known tools used in CTFs.<br>
pdf-parse showed `/Length 128` which means that it uses 128-bit key which means too big to bruteforce.

Using [qpdf](http://qpdf.sourceforge.net/) tells us the pdf is encrypted with a user password.<br>
PDFs encrypted with a user password can only be opened by providing this password.<br>
PDFs encrypted with a owner password can be opened without providing a password, but some restrictions will apply.<br>
This is additional info that this is probably not the right route.

> In the case we needed to bruteforce `John The Reaper` is a great tool and is used in another writeup to solve this challenge by supplying a wordlist.

(more on the wordlist in a moment).<br>

Since brute-forcing won't work let's go back one step and see EvlzCTF's Writeup.<br>
They used [`pdfcrack`](https://github.com/robins/pdfcrack) with wordlist `rockyou.txt`.

A wordlist is just a text file containing a list of words. Specifically here we used `rockyou.txt` a wordlist with commonly used passwords.

Alright let's try it!<br>
Install `pdfcrack`: `sudo apt install pdfcrack`.<br>
Download `rockyou.txt`: [wordlist (rockyou.txt) - GitHub](https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt).

Running `pdfcrack --wordlist=rockyou.txt encrption.pdf` reveals that the password is `butterfly`.

![the output of the command in the console](https://i.imgur.com/qVPo3hC.png)

Using the password opens the PDF.<br>
**Goal: Complete.**

## Part 3
After opening the pdf we are greeted with this:

![draconic text with image of a dragon underneath it](https://i.imgur.com/QA5f52H.png)

**Goal: Figure out what to do next.**

Using Google's Search by Image on the weird text reveals that it is *draconic text*.<br>
**New Goal: Decipher the draconic text.**

Searching Draconic Cipher gives us [dcode's website](https://www.dcode.fr/draconic-dragon-language) a popular website for deciphering ciphers.<br>
Using the website to decipher the text we get:

![](https://i.imgur.com/yRe25fX.png)

There it is... The Flag!!<br>
**Goal: Complete**

## The Flag
So the flag is `vulncon{draconic_is_cool}`.

The End.
