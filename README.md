# Install & Setup DNSCrypt-proxy in Linux

## What is DNSCrypt-proxy?

DNSCrypt is a specification that has implemented in various different softwares, such as [unbound](https://www.unbound.net/) and [dnsdist](https://dnsdist.org/). [DNSCrypt-proxy](https://github.com/DNSCrypt/dnscrypt-proxy) is another one of those.

From their github page:
> dnscrypt-proxy is a flexible DNS proxy. It runs on your computer or router, and can locally block unwanted content, reveal where your devices are silently sending data to, make applications feel faster by caching DNS responses, and improve security and confidentiality by communicating to upstream DNS servers over secure channels.

---

## Pre-Installation

* Get the files
``` Bash
$ cd $HOME
$ mkdir dnscrypt-proxy && cd /dnscrypt-proxy
$ wget https://github.com/DNSCrypt/dnscrypt-proxy/releases/download/2.1.2/dnscrypt-proxy-linux_x86_64-2.1.2.tar.gz
$ tar -xf dnscrypt-proxy-linux_x86_64-2.1.2.tar.gz
$ cp dnscrypt-proxy-linux_x86_64-2.1.2/linux-x86_64/* ../dnscrypt-proxy/
$ rm -rf dnscrypt-proxy-linux_x86_64-2.1.2/
$ cp example-dnscrypt-proxy.toml dnscrypt-proxy.toml
```
This should get you set up to start configuring the software

* Edit the config file

(Make sure you are inside the same directory as above)

``` Bash
$ echo "server_names = ['quad9-dnscrypt-ip4-filter-pri']" >> $HOME/dnscrypt-proxy/dnscrypt-proxy.toml
```
There are other server options on the webpage: [DNSCrypt-proxy server list](https://dnscrypt.info/public-servers/). You can choose one and replace the text inside **server_names**

* Test the script
``` Bash
$ ./dnscrypt-proxy
```

* Edit the resolv.conf file
Make a backup
``` Bash
$ cp /etc/resolv.conf /etc/resolv.conf.backup
```
Edit the file
``` Bash
$ sudo echo 'nameserver 127.0.0.1' >| /etc/resolv.conf
$ sudo echo 'options edns0' >> /etc/resolv.conf
```

* With the script still running, open a new terminal window
Test the script by resolving an adress
```
./dnscrypt-proxy -resolve example.com
```

* If everything went fine
Stop the script with ctrl+c

* Copy the script and config file to the proper location
``` Bash
$ sudo cp dnscrypt-proxy dnscrypt-proxy.toml /usr/bin/
```
## Installation

* Install the script as a service
``` Bash
$ cd /usr/bin
$ ./dnscrypt-proxy -service install
$ ./dnscrypt-proxy -service start
```
* Optional (might be needed)
The DHCP server might overwrite the contents of /etc/resolv.conf
If that is the case, re edit it and use the command to lock the file:
``` Bash
$ chattr +i /etc/resolv.conf
```

Note: To unlock it again:
``` Bash
$ chattr -i /etc/resolv.conf
```

* Use systemctl to see if the service is running properly
``` Bash
$ systemctl status dnscrypt-proxy.service
```
* Test that everything is working
``` Bash
$ cd /usr/bin && ./dsncrypt-proxy -resolve example.com
```

## References

[Github Page for dnscrypt-proxy](https://github.com/dnscrypt/dnscrypt-proxy/wiki/Installation-linux)
