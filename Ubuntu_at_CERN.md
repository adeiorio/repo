CERN Hacks
----------
This Gist is a guide on how to setup your computer at CERN with __Ubuntu 14.04 LTS__. 
Last tested by myself on a brand new computer on __18/11/2014__, proof tested by @[Alea88](https://github.com/Alea88).

#### NOTE (26-02-2015)
With the kernel upgrade from 3.13.0-45-generic to 3.13.0-46-generic AFS stops working:
https://bugs.launchpad.net/ubuntu/+source/openafs/+bug/1423151

Type this to check your kernel version:

```
uname -r
```

To revert the change in your system, type:

```
sudo apt-get remove 3.13.0-46-generic
sudo update-grub
```

Then restart your computer.

### How To Acces AFS 

#### Step 1: install packages


Download the following:

```
sudo module-assistant prepare openafs-modules; module-assistant auto-build openafs-modules; dpkg -i /usr/src/openafs-modules-*.deb; apt-get install openafs-client; apt-get install openafs-modules-dkms; apt-get install openafs-krb5; apt-get install krb5-user; apt-get install krb5-config
sudo service openafs-client start
```

#### Step 2: generate your keytab

Make sure that you don't have any __.keytab__ created yet. If so, erase it. If you want to check whats in your keytab type:

```
$ ktutil
ktutil: read_kt path_to_keytab/name_of_keytab
ktutil: list
```
Execute this commands, with your username:
```
$ ktutil
ktutil: addent -password -p ansantam@CERN.CH -k 1 -e aes256-cts
ktutil: addent -password -p ansantam@CERN.CH -k 1 -e arcfour-hmac-md5
ktutil: wkt .keytab
ktutil: q
```

See if your keytab works:
```
kinit -kt .keytab ansantam
```

If nothing appears in your prompt, it works. Move your __.keytab__ file to __/etc/__ and rename it __krb5.keytab__.

#### Step 3: use "cern.ch" as default AFS cell

Open the file __/etc/openafs/ThisCell__ and add ``cern.ch`` 

#### Step 4: setup kerberos5 authentication

Open the file __/etc/krb5.conf__ and replace it by [this one](http://linux.web.cern.ch/linux/docs/krb5.conf)


#### Step 5: avoiding SSH errors

To avoid problems when connecting to lxplus with:
```
ssh user@lxplus
```
open the file __/etc/ssh/ssh_config__ and make it look like this:

```
HOST lxplus*
    ForwardX11 yes
    ForwardX11Trusted no
    GSSAPITrustDNS yes
    HashKnownHosts yes
    GSSAPIAuthentication yes
    GSSAPIDelegateCredentials yes
```

#### Step 6: renew your token automatically

Open the file __/etc/crontab__ and add the following line:

```
@daily ID=afstoken kinit --renew
```

#### Step 7: time sync

Kerberos only works if your computer clock is in close sync (certainly within 5 minutes) with CERN time servers. 

In a normal computer you can do a one-off clock sync like typing the following:

```
ntpdate ntp.ubuntu.com
```

For CERN you'll need to install the ntp daemon, which will continuously keep your clock in accurate sync with the CERN time servers.

```
apt-get install ntp
```

Then add the following lines to your __/etc/ntp.conf__ file, and comment or erase the lines for ubuntu time servers:
```
# CERN Client
                   
server 137.138.18.69 version 4 #IP-TIME-0
server 137.138.16.69 version 4 #IP-TIME-1
server 137.138.17.69 version 4 #IP-TIME-2
                   
# Disable remote access, but trust sources of time restrict default nomodify #noquery
restrict default nomodify noquery

# Allow hosts to query stats and ask for the time.
# eg restrict 123.123.123.123 nomodify

# Allow localhost to do everything.
restrict 127.0.0.1

#logconfig=all
```

More information [here](http://service-ntp.web.cern.ch/service-ntp/clientconfig.asp). Then restart the ntp service:

```
sudo service ntp restart
```

#### Step 8: automatize identification

My way of obtaining my credentials is adding the following line in my __.bash_aliases__ file: 

```
alias afs="kdestroy && kinit -kt /etc/krb5.keytab ansantam -l 7d -r 1d ; aklog CERN.CH"
```
Like this I can obtain the credentials only by typping __"afs"__ in the console.

What all of this means:

* The __"kdestroy"__ command deletes a Kerberos credentials cache file. More info [here](http://publib.boulder.ibm.com/infocenter/aix/v7r1/index.jsp?topic=%2Fcom.ibm.aix.cmds%2Fdoc%2Faixcmds3%2Fkdestroy.htm)

* The __"kinit"__ command is used to get a Kerberos ticket. See __"kinit"__'s options [here](http://linux.die.net/man/1/kinit). We are using the keytab we just created to obtain the Kerberos ticket.

* The __"aklog"__ command is used to obtain tokens for authentication to AFS. More info [here](http://docs.openafs.org/Reference/1/aklog.html).


#### Debugging

* Restart the console so your alias will work.
* Restart the AFS client: ``sudo service openafs-client restart``.
* Login with the alias you chose (make sure you get ticket and token).
* Make sure the clocks are synced.


#### References

* [Ome Gak](https://gist.github.com/OmeGak/9530124)
* [linux.web.cern.ch](http://linux.web.cern.ch/linux/docs/kerberos-access.shtml)
* [akorneev.web.cern.ch](http://akorneev.web.cern.ch/akorneev/howto/openafs.txt)
* [Yngve's blog](http://eothred.wordpress.com/2011/05/09/cern-and-kerberos-tokens/)

### How To Install a CERN Printer
- Open the printer settings on your local computer.
- Click "Add"
- Select "Network printer" --> "LPD/LPR Host or printer"
- Look for the name of your printer [here](https://printservice.web.cern.ch/printservice/UserTools/PrinterList.aspx)
- Write __name.print.cern.ch__ in the field "Host" and __name__ in "Queue" and press "Forward"
- Select brand and model of the printer
- Select the duplexer if it can print both sides of the paper
- Change the name of the printer to the original name provided by the CERN page provided above
- Print a test page
- Done!

### Keep lxplus alive
```
sudo emacs /etc/ssh/ssh_config
ServerAliveInterval 60
ServerAliveCountMax 2
```
