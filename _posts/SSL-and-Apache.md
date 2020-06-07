---
published: true
---
## Reference for SSL in Apache for a local instance

![openssl.png]({{site.baseurl}}/_posts/openssl.png)

Needed a self generated SSL certificate in Apache to prepare a PoC. Firstable in Debian 10 Apache isn't installed by default, so after installing it with apt-get apache2 I followed [this post](https://web.archive.org/web/20200607175202/https://www.ostechnix.com/configure-apache-virtual-hosts-ubuntu-part-1/ "Setting up virtualhosts") to setup a Virtual Host. Then I found this [blogpost](https://web.archive.org/web/20200606214103/https://kifarunix.com/how-to-create-self-signed-ssl-certificate-with-mkcert-on-ubuntu-18-04/), very useful, where basically is using [MKCERT](https://github.com/FiloSottile/mkcert) to generate locally a certificate without getting troubles without a lot arcane commands.  

But in the process when tried to restart the HTTP service I had this message:
(98)Address already in use: AH00072: make_sock: could not bind to address [::]:443
(98)Address already in use: AH00072: make_sock: could not bind to address 0.0.0.0:443

had troubles because somehow I had another service using same port, I saw this message:

