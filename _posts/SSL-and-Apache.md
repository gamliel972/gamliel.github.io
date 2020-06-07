---
published: true
---
## Reference for SSL in Apache for a local instance

![openssl.png]({{site.baseurl}}/_posts/openssl.png)

Needed a self generated SSL certificate in Apache to prepare a PoC. Firstable in Debian 10 Apache isn't installed by default, so after installing it with **apt-get apache2** I followed [this post](https://web.archive.org/web/20200607175202/https://www.ostechnix.com/configure-apache-virtual-hosts-ubuntu-part-1) to setup a Virtual Host. Then I found this [blogpost](https://web.archive.org/web/20200606214103/https://kifarunix.com/how-to-create-self-signed-ssl-certificate-with-mkcert-on-ubuntu-18-04/), very useful, where basically is using [MKCERT](https://github.com/FiloSottile/mkcert) to generate locally a certificate without getting troubles without alot of arcane commands.  

But in the process when tried to restart the HTTP service I had this message:

```
$ sudo systemctl status apache2.service 
● apache2.service - The Apache HTTP Server
   Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor preset: enabled)
   Active: failed (Result: exit-code) since Sat 2020-06-06 12:31:14 CST; 2min 35s ago
     Docs: https://httpd.apache.org/docs/2.4/
  Process: 25203 ExecStart=/usr/sbin/apachectl start (code=exited, status=1/FAILURE)

Jun 06 12:31:14 hunter systemd[1]: Starting The Apache HTTP Server...
Jun 06 12:31:14 hunter apachectl[25203]: (98)Address already in use: AH00072: make_sock: could not bind to address [::]:443
Jun 06 12:31:14 hunter apachectl[25203]: (98)Address already in use: AH00072: make_sock: could not bind to address 0.0.0.0:443
Jun 06 12:31:14 hunter apachectl[25203]: no listening sockets available, shutting down
Jun 06 12:31:14 hunter apachectl[25203]: AH00015: Unable to open logs
Jun 06 12:31:14 hunter apachectl[25203]: Action 'start' failed.
Jun 06 12:31:14 hunter apachectl[25203]: The Apache error log may have more information.
Jun 06 12:31:14 hunter systemd[1]: apache2.service: Control process exited, code=exited, status=1/FAILURE
Jun 06 12:31:14 hunter systemd[1]: apache2.service: Failed with result 'exit-code'.
Jun 06 12:31:14 hunter systemd[1]: Failed to start The Apache HTTP Server.
```


**(98)Address already in use: AH00072: make_sock: could not bind to address [::]:443**

**(98)Address already in use: AH00072: make_sock: could not bind to address 0.0.0.0:443**

Because somehow I had another service using same port, then the command that helped me to realize how to detected what process was using the same port is this:

**netstat -ltnp | grep :80**

That I found in this question in [Ask Ubuntu](https://askubuntu.com/questions/277162/apache-fails-to-start-address-already-in-use-but-not-really).
