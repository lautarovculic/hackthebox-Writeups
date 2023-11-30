# Pilgrimage - Hack The Box - @lautarovculic

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled.png)

****************Difficult:**************** Easy

****************Release:**************** 24/06/2023

****IP:**** 10.10.11.219

******OS:****** Linux

---

# User.txt

First and as in most HTB machines, we are going to configure the /**etc/hosts** file.

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%201.png)

Let's see what **nmap** tells us.

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%202.png)

We have port **22** and **80** running.

Let's run **dirb** on the **web address** to see if there are any *interesting directories* while looking at the **content of the web page**.

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%203.png)

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%204.png)

On the one hand we have the **/.git/ directory** and on the other hand we see that we could try to **upload malicious files**.

Let's use the **git-dump tool** [https://github.com/arthaud/git-dumper](https://github.com/arthaud/git-dumper)

And we will see what **interesting files** the host has.

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%205.png)

Let's see what's in the **.php files**.

Testing the file upload, we see that the web uploads the images **to the path** [http://pilgrimage.htb/shrunk/](http://pilgrimage.htb/shrunk/)

After analyzing the files we found a path wherein it is mentioned **magick is used to convert** the images.

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%206.png)

**************index.php**************

We find the version of **magick being used**, so we will look for a **suitable exploit**.

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%207.png)

I found the following vulnerability, it may work for us.

ImageMagick is vulnerable to information disclosure. *When it parses a PNG image*, the resulting image could have *embedded the content of an arbitrary remote file*.
[https://github.com/Sybil-Scan/imagemagick-lfi-poc](https://github.com/Sybil-Scan/imagemagick-lfi-poc)

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%208.png)

Now, we **will upload the exploit.png** and it **will be downloaded** (with the */etc/passwd information we asked for*).

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%209.png)

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%2010.png)

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%2011.png)

Saved as **output.png**

And with the command **identify -verbose output.png** we will find *the content*, which will be passed to **CyberChef for decoding**.

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%2012.png)

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%2013.png)

Nice!!

We found the user "**emily**".

We will **continue to look** for something *interesting in the .php content* we downloaded a few minutes ago.

In the **dashboard.php** file we find a **database PATH**.

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%2014.png)

Now, we **will generate another image again** but **changing the path** we want to see. In this case it would be:

```bash
python3 [generate.py](http://generate.py/) -f "/var/db/pilgrimage" -o exploit.png
```

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%2015.png)

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%2016.png)

We **download the new exploit.png** that we uploaded, we call it **output.png** and with the command **identify** we *extract the content again*.

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%2017.png)

It is longer than the previous one, we will go through **CyberChef**.

We find next to *our user created in the pilgrimage.htb page* the **user** and **password** of **emily**.

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%2018.png)

**Username** = emily

**Password** = abigchonkyboi123

We are going to connect **via SSH** with these credentials.

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%2019.png)

And we get the ****************user.txt**************** flag.

# Root.txt

With **ps -aux** we will see which *processes are running on the machine*.

Using the **ps** command we find that “**/usr/sbin/malwarescan.sh**” this was a script running in the background that uses ***binwalk**.*

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%2020.png)

The version was **Binwalk v2.3.2**.

Then search to find exploit on binwalk and here what I found:

[https://www.exploit-db.com/exploits/51249](https://www.exploit-db.com/exploits/51249)

Let's download it.

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%2021.png)

The use of this script is:

```bash
python3 [exploit.py](http://exploit.py/) [-h] file ip port
```

We will use the generated file named **binwalk_exploit.png**

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%2022.png)

In addition, we will leave **rlwrap listening on port 7777**

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%2023.png)

To **transfer the exploit** (.png) we are going to raise a **python web server on port 8000**.

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%2024.png)

And on the other hand, with the vulnerable machine we will download it.

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%2025.png)

Now on the *victim machine* we **copy the .png** to **/var/www/pilgrimage.htb/shrunk/**

We *wait a moment* and in the *rlwrap connection* we **will have root access**, and we can get **root.txt**

![Untitled](Pilgrimage%20-%20Hack%20The%20Box%20-%20@lautarovculic%20d3163ca1a8f74f20b8c40a462248c6c5/Untitled%2026.png)

I hope you found it useful (: