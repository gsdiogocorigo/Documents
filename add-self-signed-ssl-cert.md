# Add Self-Signed SSL Certificate in XAMPP

1. Go to **C:\xampp\apache\**
2. Create a folder **crt**
3. Create a file **cert.conf** with the code:
```conf
[ req ]

default_bits        = 2048
default_keyfile     = server-key.pem
distinguished_name  = subject
req_extensions      = req_ext
x509_extensions     = x509_ext
string_mask         = utf8only

[ subject ]

countryName                 = Country Name (2 letter code)
countryName_default         = US

stateOrProvinceName         = State or Province Name (full name)
stateOrProvinceName_default = NY

localityName                = Locality Name (eg, city)
localityName_default        = New York

organizationName            = Organization Name (eg, company)
organizationName_default    = Example, LLC

commonName                  = Common Name (e.g. server FQDN or YOUR name)
commonName_default          = {{DOMAIN}}

emailAddress                = Email Address
emailAddress_default        = test@example.com

[ x509_ext ]

subjectKeyIdentifier   = hash
authorityKeyIdentifier = keyid,issuer

basicConstraints       = CA:FALSE
keyUsage               = digitalSignature, keyEncipherment
subjectAltName         = @alternate_names
nsComment              = "OpenSSL Generated Certificate"

[ req_ext ]

subjectKeyIdentifier = hash

basicConstraints     = CA:FALSE
keyUsage             = digitalSignature, keyEncipherment
subjectAltName       = @alternate_names
nsComment            = "OpenSSL Generated Certificate"

[ alternate_names ]

DNS.1       = {{DOMAIN}}
```
4. Change the **{{DOMAIN}}** to your webisite (Ex: site.test)
5. Create another file **makecert.bat** with the code:
```bat
@echo off
set /p domain="Enter Domain: "
set OPENSSL_CONF=../conf/openssl.cnf

if not exist .\%domain% mkdir .\%domain%

..\bin\openssl req -config cert.conf -new -sha256 -newkey rsa:2048 -nodes -keyout %domain%\server.key -x509 -days 365 -out %domain%\server.crt

echo.
echo -----
echo The certificate was provided.
echo.
pause
```
6. Run the file **makecert.bat** and provide your website name (Ex: site.test)
7. Click **Enter** on all other information
8. The bat file creates a new folder with the name of your website
9. Open the folder and install the **.crt** file
10. Click: Install Certificate > Next > Local Machine > Next > Place all certificates in the following store > Browse > Trusted Root Certification Authorities > OK > Next > Finish > OK > OK
11. Go to XAMPP Control Panel and click on the Apache section: Config > httpd-xampp.conf and paste the following code at the bottom:
```conf
<VirtualHost *:80> # CHANGE PORT IF NEEDED
	DocumentRoot "$PATH$" # REPLACE WITH THE PATH TO THE PROJECT
	ServerName $WEBSITE$ # CHANGE TO THE WEBSITE NAME INSERTED IN THE PREVIOUS STEPS
	ServerAlias *.$WEBSITE$ # CHANGE TO THE WEBSITE NAME INSERTED IN THE PREVIOUS STEPS
</VirtualHost>

<VirtualHost *:443> # CHANGE PORT IF NEEDED
	DocumentRoot "$PATH$" # REPLACE WITH THE PATH TO THE PROJECT
	ServerName $WEBSITE$ # CHANGE TO THE WEBSITE NAME INSERTED IN THE PREVIOUS STEPS
	ServerAlias *.$WEBSITE$ # CHANGE TO THE WEBSITE NAME INSERTED IN THE PREVIOUS STEPS
	SSLEngine on
	SSLCertificateFile "crt/$WEBSITE$/server.crt" # CHANGE TO THE WEBSITE NAME INSERTED IN THE PREVIOUS STEPS
	SSLCertificateKeyFile "crt/$WEBSITE$/server.key" # CHANGE TO THE WEBSITE NAME INSERTED IN THE PREVIOUS STEPS
</VirtualHost>
```
12. Restart Apache in Xampp Control Panel
