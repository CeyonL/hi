# NetOps

Uhh, the website is no longer available, so I’ll write down how I remember solving this one.

There was a login page, and a note at the bottom said that the database used SQLite. This made me think of SQL injection, so I tried different inputs in both the username and password fields.

```text
‘ OR 1=1;--
```

```text
‘ OR 1=1;
```

```text
‘ OR  ‘1’=’1’;--
```

```text
‘ OR  ‘1’=’1’;
```

```text
‘ OR  ‘1’=’1;--
```

```text
‘ OR ‘1’=’1;
```

One of them worked, but I forgot which one. If I remember correctly, I logged in to a guest account.

After that, there was a ping feature. The command looked something like “ping -t 1 -c 1 <command>”. I tried using &, &&, |, and ;, but they all seemed to be filtered. So I searched for command injection on Google and went to PayloadsAllTheThings.

![NetOps — PayloadsAllTheThings reference](wu-markdown-assets/netops/image1.png)

This technique caught my eye, so I immediately tested it with `ping -t 1 -c 1 127.0.0.0$(pwd)`.

NOTE: I recreated a similar example with AI, but it is not an exact copy.

![NetOps — local AI-assisted reconstruction, not original server evidence](wu-markdown-assets/netops/image2.png)

The ping output looked similar to this, but in the original output, the injected text appeared in the “PING 1.1.1.1 (1.1.1.1) 56(84) bytes of data.” line.

So it should look like this:

```text
“PING 1.1.1.1 (1.1.1.1) 56(84) bytes of data /home/vihi.
```

```text
64 bytes from 1.1.1.1: icmp_seq=1 ttl=64 time=357 ms
```

```text
--- 1.1.1.1 ping statistics ---
```

```text
1 packets transmitted, 1 received, 0% packet loss, time 0ms
```

```text
rtt min/avg/max/mdev = 356.767/356.767/356.767/0.000 ms
```

Then I tried `ping -t 1 -c 1 127.0.0.0$(whoami)` and `ping -t 1 -c 1 127.0.0.0$(ls ../..)`.

However, I received an “Argument list too long” or “File name too long” error. At the time, I thought that:

```text
“PING 1.1.1.1 (1.1.1.1) 56(84) bytes of data /home/vihi.
```

```text
64 bytes from 1.1.1.1: icmp_seq=1 ttl=64 time=357 ms
```

```text
--- 1.1.1.1 ping statistics ---
```

```text
1 packets transmitted, 1 received, 0% packet loss, time 0ms
```

rtt min/avg/max/mdev = 356.767/356.767/356.767/0.000 ms” was the maximum amount of output it could print, and that injecting a command would only produce one line of output. I thought that if a directory contained more than two subdirectories or files, such as “/home/hi /home/bye”, it would cause an error. BUT if I removed 1.1.1.1 or 127.0.0.0, I could get much more output. (I don’t know why they allowed this without filtering it, but alright.) So `ping -t 1 -c 1 $(ls)` would return a result instead of an error, even with more than two files. I then spent about an hour searching in /root (which gave me a permission error), /var, and /tmp, and checking /etc/passwd, but I didn’t find anything. Luckily, using `$(find ../.. -xdev -type f -name “*flag*”)` returned a suspicious path:

```text
/opt/.hidden_flag
```

So I used `$(cat ../../opt/.hidden_flag)`, and the flag appeared.

## Conclusion

Based on my recollection, the intended path was to get past the login page and then use command substitution through the ping feature to locate and read the flag file. I eventually found /opt/.hidden_flag and read it to obtain the flag. However, I no longer have the exact successful login payload, and the original website is unavailable, so I cannot verify every detail of this reconstruction. The explanation above about output limits reflects what I thought at the time, not a confirmed technical cause of the errors. The main takeaway is that user-controlled input must be handled safely in both database queries and system-command execution.
