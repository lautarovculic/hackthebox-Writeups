# Cat - Hack The Box - @lautarovculic

![Untitled](Cat%20-%20Hack%20The%20Box%20-%20@lautarovculic%2085148dd6324647b89bec258f18b7e6e0/Untitled.png)

****************Difficult: Easy****************

****************Release:**************** 20/03/2020

****IP:**** -

******OS:****** Android

---

*There is no User.txt or Root.txt here.
We simply have to download the file that the platform gives us and find a flag with the format HTB{flag}*

Download the file, note that it is a **.zip** file
Once unzipped, we will have the following file: **Cat.ab**

![Untitled](Cat%20-%20Hack%20The%20Box%20-%20@lautarovculic%2085148dd6324647b89bec258f18b7e6e0/Untitled%201.png)

The password to decompress the file is provided by HTB and is **hackthebox**

“*An Android .ab file is a backup of your data and settings for either an app or the entire system. It's like a kind of "snapshot" that saves important information such as contacts, messages, apps, and more, so you can restore them if something goes wrong or if you need to transfer everything to another device. It's a useful way to keep your data safe and make changes to your device without losing important information”.* - ChatGPT 😆

We must unpack the archive, I found the following command on stackoverflow:

**( printf "\x1f\x8b\x08\x00\x00\x00\x00\x00" ; tail -c +25 cat.ab ) | tar xfvz -**

![Untitled](Cat%20-%20Hack%20The%20Box%20-%20@lautarovculic%2085148dd6324647b89bec258f18b7e6e0/Untitled%202.png)

The command performs the following: it outputs an empty GZ archive header, then takes the contents of the "cat.ab" file starting from byte 25, compresses it into GZ format and extracts the files from the simulated archive. The final result will be the extraction of files from "cat.ab" after omitting the first 24 bytes.

![Untitled](Cat%20-%20Hack%20The%20Box%20-%20@lautarovculic%2085148dd6324647b89bec258f18b7e6e0/Untitled%203.png)

Inspecting **inside the folders** that we unzipped, we can see that at the **end of the clipboard** of the image located in **/shared/0/Pictures/IMAG0004.jpg** 👀 {Something interesting} 👀

I hope you found it useful (: