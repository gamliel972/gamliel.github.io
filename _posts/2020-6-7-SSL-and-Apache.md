---
published: true
title: Reference for SSL in Apache for a local instance
layout: post
---

![openssl.png]({{site.baseurl}}/images/openssl.png)

Needed a self generated SSL certificate in Apache to prepare a PoC. Firstable in Debian 10 Apache isn't installed by default, so after installing it with **apt-get install apache2** I followed [this post](https://web.archive.org/web/20200607175202/https://www.ostechnix.com/configure-apache-virtual-hosts-ubuntu-part-1) to setup a Virtual Host. Then I found this [blogpost](https://web.archive.org/web/20200606214103/https://kifarunix.com/how-to-create-self-signed-ssl-certificate-with-mkcert-on-ubuntu-18-04/), very useful, where basically is using [MKCERT](https://github.com/FiloSottile/mkcert) to generate locally a certificate without getting troubles without alot of arcane commands.  

But in the process when tried to restart the HTTP service I had this message:

```
$ sudo service apache2 start
Job for apache2.service failed because the control process exited with error code.
See **systemctl status apache2.service** and **journalctl -xe** for details.
```

Knowing the status:
```
$ sudo systemctl status apache2.service 
● apache2.service - The Apache HTTP Server
   Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor preset: enabled)
   Active: failed (Result: exit-code) since Sat 2020-06-06 21:31:14 CST; 2min 35s ago
     Docs: https://httpd.apache.org/docs/2.4/
  Process: 25203 ExecStart=/usr/sbin/apachectl start (code=exited, status=1/FAILURE)

Jun 06 21:31:14 hunter systemd[1]: Starting The Apache HTTP Server...
Jun 06 21:31:14 hunter apachectl[25203]: (98)Address already in use: AH00072: make_sock: could not bind to address [::]:443
Jun 06 21:31:14 hunter apachectl[25203]: (98)Address already in use: AH00072: make_sock: could not bind to address 0.0.0.0:443
Jun 06 21:31:14 hunter apachectl[25203]: no listening sockets available, shutting down
Jun 06 21:31:14 hunter apachectl[25203]: AH00015: Unable to open logs
Jun 06 21:31:14 hunter apachectl[25203]: Action 'start' failed.
Jun 06 21:31:14 hunter apachectl[25203]: The Apache error log may have more information.
Jun 06 21:31:14 hunter systemd[1]: apache2.service: Control process exited, code=exited, status=1/FAILURE
Jun 06 21:31:14 hunter systemd[1]: apache2.service: Failed with result 'exit-code'.
Jun 06 21:31:14 hunter systemd[1]: Failed to start The Apache HTTP Server.
```

The key problem was:
```
(98)Address already in use: AH00072: make_sock: could not bind to address [::]:443

(98)Address already in use: AH00072: make_sock: could not bind to address 0.0.0.0:443
```

Somehow I had another service using same port, then the command that helped me to realize how to detect what process interfered with HTTP restart:

**netstat -ltnp | grep :80**

I found in this question on [Ask Ubuntu](https://askubuntu.com/questions/277162/apache-fails-to-start-address-already-in-use-but-not-really) the answer to the issue I was facing. And the solution is kill the process that uses the same port, 443 in my case.

```
$ sudo netstat -ltnp | grep :443
tcp        0      0 0.0.0.0:443             0.0.0.0:*               LISTEN      1884 %2F Apache2   
tcp6       0      0 :::443                  :::*                    LISTEN      1884 %2F Apache2
```

```
$ sudo kill -9 1884
$ sudo systemctl start apache2.service 
$ sudo systemctl status apache2.service 
● apache2.service - The Apache HTTP Server
   Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor preset: enabled)
   Active: active (running) since Sat 2020-06-06 21:44:35 CST; 50s ago
     Docs: https://httpd.apache.org/docs/2.4/
  Process: 26192 ExecStart=/usr/sbin/apachectl start (code=exited, status=0/SUCCESS)
 Main PID: 26197 (apache2)
    Tasks: 6 (limit: 4915)
   Memory: 15.1M
   CGroup: /system.slice/apache2.service
           ├─26197 /usr/sbin/apache2 -k start
           ├─26198 /usr/sbin/apache2 -k start
           ├─26199 /usr/sbin/apache2 -k start
           ├─26200 /usr/sbin/apache2 -k start
           ├─26201 /usr/sbin/apache2 -k start
           └─26202 /usr/sbin/apache2 -k start

Jun 06 21:44:35 hunter systemd[1]: Starting The Apache HTTP Server...
Jun 06 21:44:35 hunter systemd[1]: Started The Apache HTTP Server.
```

That's it, problem fixed!
