# Topology - Hack The Box - @lautarovculic

![Untitled](Topology%20-%20Hack%20The%20Box%20-%20@lautarovculic%200de49385b4084259ac32e2056674f72a/Untitled.png)

****************Difficult:**************** Easy

****************Release:**************** 10/06/2023

****IP:**** 10.10.11.217

******OS:****** Linux

---

# User.txt

First of all we will configure our **/etc/hosts** file.

![Untitled](Topology%20-%20Hack%20The%20Box%20-%20@lautarovculic%200de49385b4084259ac32e2056674f72a/Untitled%201.png)

After that, we will do our **scan with nmap** to do a reconnaissance of the machine.

![Untitled](Topology%20-%20Hack%20The%20Box%20-%20@lautarovculic%200de49385b4084259ac32e2056674f72a/Untitled%202.png)

We note that we have port **22** and **80** *open*.

Let's investigate port 80.

![Untitled](Topology%20-%20Hack%20The%20Box%20-%20@lautarovculic%200de49385b4084259ac32e2056674f72a/Untitled%203.png)

We come across a web page, and if we search, we will **find a link about a project**.

![Untitled](Topology%20-%20Hack%20The%20Box%20-%20@lautarovculic%200de49385b4084259ac32e2056674f72a/Untitled%204.png)

This project takes us to [http://latex.topology.htb/equation.php](http://latex.topology.htb/equation.php) so we will **add it** to our **/etc/hosts** file.

![Untitled](Topology%20-%20Hack%20The%20Box%20-%20@lautarovculic%200de49385b4084259ac32e2056674f72a/Untitled%205.png)

Let's see what's inside.

![Untitled](Topology%20-%20Hack%20The%20Box%20-%20@lautarovculic%200de49385b4084259ac32e2056674f72a/Untitled%206.png)

After some research, I have seen that there are vulnerabilities of **injections** in **LaTeX**. So we will try to make some of them.

[https://book.hacktricks.xyz/pentesting-web/formula-doc-latex-injection](https://book.hacktricks.xyz/pentesting-web/formula-doc-latex-injection?source=post_page-----1e4cf07d7805--------------------------------)

![Untitled](Topology%20-%20Hack%20The%20Box%20-%20@lautarovculic%200de49385b4084259ac32e2056674f72a/Untitled%207.png)

Let's try **$\lstinputlisting{/etc/passwd}$**

![Untitled](Topology%20-%20Hack%20The%20Box%20-%20@lautarovculic%200de49385b4084259ac32e2056674f72a/Untitled%208.png)

Nice!

We can try the following command to see if we get a **user** and a **hash**:

**$\lstinputlisting{/var/www/dev/.htpasswd}$**

![Untitled](Topology%20-%20Hack%20The%20Box%20-%20@lautarovculic%200de49385b4084259ac32e2056674f72a/Untitled%209.png)

We have the user **vdaisley** and the hash **$apr1$1ONUB/S2$58eeNVirnRDB5zAIbIxTY0**

Let's crack it with **hashcat**.

```bash
hashcat -a 0 -m 1600 hash.txt /home/kali/Downloads/rockyou.txt
```

![Untitled](Topology%20-%20Hack%20The%20Box%20-%20@lautarovculic%200de49385b4084259ac32e2056674f72a/Untitled%2010.png)

And the password is:

**calculus20**

![Untitled](Topology%20-%20Hack%20The%20Box%20-%20@lautarovculic%200de49385b4084259ac32e2056674f72a/Untitled%2011.png)

Now that we have the **username** and **password**, let's try to log in via **SSH**.

![Untitled](Topology%20-%20Hack%20The%20Box%20-%20@lautarovculic%200de49385b4084259ac32e2056674f72a/Untitled%2012.png)

Let's look for the file **user.txt**

![Untitled](Topology%20-%20Hack%20The%20Box%20-%20@lautarovculic%200de49385b4084259ac32e2056674f72a/Untitled%2013.png)

# Root.txt

We can use **pspy** to **view the processes** without *root permissions*.

[https://github.com/DominicBreuker/pspy](https://github.com/DominicBreuker/pspy?source=post_page-----1e4cf07d7805--------------------------------)

```bash
./pspy64
```

![Untitled](Topology%20-%20Hack%20The%20Box%20-%20@lautarovculic%200de49385b4084259ac32e2056674f72a/Untitled%2014.png)

Within this context, a discernible process pertains to the execution of files bearing the “***.plt**” extension. This operation transpires within the specific directory path designated as “**/opt/gnuplot/.**”

![Untitled](Topology%20-%20Hack%20The%20Box%20-%20@lautarovculic%200de49385b4084259ac32e2056674f72a/Untitled%2015.png)

I found the following page where it shows us an **exploit** to do a **privilege escalation**.
[https://exploit-notes.hdks.org/exploit/linux/privilege-escalation/gnuplot-privilege-escalation/](https://exploit-notes.hdks.org/exploit/linux/privilege-escalation/gnuplot-privilege-escalation/)

And we will use the following command:

```bash
echo "system 'chmod u+s /bin/bash'" > /opt/gnuplot/pwn.plt
```

![Untitled](Topology%20-%20Hack%20The%20Box%20-%20@lautarovculic%200de49385b4084259ac32e2056674f72a/Untitled%2016.png)

Finally, we run **/bin/bash -p** and we can find the **root.txt** file.

I hope you found it useful (: