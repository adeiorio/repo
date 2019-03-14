Setup local Environment and prepare user analysis code.

### How to use your certificate with grid-proxy-init.
--> mkdir .globus

--> cd .globus
    Copy the above PKCS#12 cert.p12 file to the computer where you will run grid-proxy-init.

--> openssl pkcs12 -in cert.p12 -clcerts -nokeys -out $HOME/.globus/usercert.pem
    Enter Import Password and then

--> openssl pkcs12 -in cert.p12 -nocerts -out $HOME/.globus/userkey.pem
    Type ls ---- one must have three files 1223.p12*, usercert.pem, userkey.pem

    Type the following to see ur certifcate
--> openssl x509 -in usercert.pem -noout -text | less

    Make sure that userkey.pem has valid permissions --600
    -rw-r--r-- 1 wajid zh 3146 Mar 24 14:30 cert.p12
    -rw-r--r-- 1 wajid zh 2192 Mar 24 14:30 usercert.pem
    -rw-r--r-- 1 wajid zh 1900 Mar 24 14:31 userkey.pem

    Change the permissions to
--> chmod 600 userkey.pem
    -rw-r--r-- 1 wajid zh 3146 Mar 24 14:30 cert.p12
    -rw-r--r-- 1 wajid zh 2192 Mar 24 14:30 usercert.pem
    -rw------- 1 wajid zh 1900 Mar 24 14:31 userkey.pem
