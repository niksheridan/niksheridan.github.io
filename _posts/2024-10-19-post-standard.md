---
title: "SSL Quick reference"
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - Post Formats
  - How To
  - readability
  - standard
---
How to's and reminders on syntax and parameters.

## Generate CA

```bash
# new key
openssl genrsa \
  -aes256 \
  -out dishclothCA.20151108.key 4096
# new self signed request
openssl req -config openssl.cnf \
  -key dishclothCA.20151108.key \
  -new \
  -x509 \
  -days 7300 \
  -sha256 \
  -extensions v3_ca \
  -out dishclothCA.20151108.cer
```

## Generate Self Signed Certificate and Export to pfx (PKCS12)

```bash
openssl req \
  -x509 \
  -sha256\
   -nodes -days 365 -newkey rsa:2048 -keyout azure.key -out azure.crt
openssl pkcs12 -export -out azure.pfx -inkey azure.key -in azure.crt 
```

## Generate CSR

```bash
# new key
openssl genrsa \
  -out nsheridan.plus.com.20151108.key 2048
# new request
openssl req \
  -new \
  -sha256 \
  -key nsheridan.plus.com.20151108.key \
  -out nsheridan.plus.com.20151108.csr \
  -config openssl.cnf
```

## Adding SANs

Make sure this section is added to the req section - make these changes to your config file (default being /etc/ssl/openssl.cnf

```bash
req_extensions = v3_req
```

Context:

```bash
[ req ]
default_bits= 1024
default_keyfile = privkey.pem
distinguished_name  = req_distinguished_name
attributes  = req_attributes
x509_extensions = v3_ca # The extentions to add to the self signed cert
req_extensions = v3_req
```

Then your SANs (add under the SET-ex3):

```bash
# SET-ex3   = SET extension number 3

[alt_names]
DNS.1 = meetups.dishcloth.com
DNS.2 = meetupshq.dishcloth.com
DNS.3 = meetupsdr.dishcloth.com
IP.1 = 1.1.1.1
```

If you are using openssl as a CA make sure you copy the extensions (use with care!)

```bash
# Extension copying option: use with caution.
copy_extensions = copy
```

Sign it with an extensions file:

```bash
openssl x509 -req \
  -days 3650 \
  -in meetups.dishcloth.com.20160330.csr \
  -CA LDN4IV1PRV01_CA.20160330.cer \
  -CAkey LDN4IV1PRV01_CA.20160330.key \
  -CAcreateserial \
  -out meetups.dishcloth.com.20160330.cer \
  -extensions v3_req \
  -extfile meetups.dishcloth.com.20160330.cnf
```

## Check CSR

```bash
openssl req \
  -noout \
  -text \
  -in nsheridan.plus.com.20151108.csr
```

## Sign CSR

```bash
openssl x509 \
  -req \
  -days 3650 \
  -in nsheridan.plus.com.20151108.csr \
  -CA dishclothCA.20151108.cer \
  -CAkey dishclothCA.20151108.key \
  -CAcreateserial \
  -out nsheridan.plus.com.20151108.cer
```

## Check CER

```bash
openssl x509 \
  -text \
  -in nsheridan.plus.com.20151108.cer
```

## Convert DER to PEM

```bash
openssl x509 \
  -inform der \
  -in certificate.cer \
  -out certificate.pem
```

## Convert PEM to DER

```bash
openssl x509 \
  -outform der \
  -in certificate.pem \
  -out certificate.der
```

## Check OCSP Validity

Note the CERT_CHAIN is the PEM format certificate with the root at the bottom and intermediates at the top of the file (literally) with the last intermediate in the chain at the top.

```bash
openssl ocsp \
  -issuer ./CERT_CHAIN \
  -cert WILD.dishcloth.com.20140415.cer \
  -text \
  -url http://ocsp.dishcloth.com/ocsp
```

## Client Certificates

[https://gist.github.com/mtigas/952344](https://gist.github.com/mtigas/952344)

* Generate a key
* Generate a CSR with the key
* Sign the CSR with the CA certificate and key file
* Wrap it all up into a P12 for importing into a workstation

```bash
sudo openssl genrsa \
  -aes256 \
  -out ./sheridannet.20150827.LAB.key 4096
sudo openssl req \
  -new \
  -key ./sheridannet.20150827.LAB.key \
  -out ./sheridannet.20150827.LAB.csr
sudo openssl ca \
  -in ./sheridannet.20150827.LAB.csr \
  -cert ./labCA.cer \
  -keyfile labCA.key \
  -out ./sheridannet.20150827.LAB.cer
sudo openssl pkcs12 \
  -export \
  -clcerts \
  -in ./sheridannet.20150827.LAB.cer \
  -inkey ./sheridannet.20150827.LAB.key \
  -out ./sheridannet.20150827.LAB.p12
```

## SSL Scan with Client Certificate Authentication

Note the escape character before the `!`

```bash
sslscan \
  --pk="sheridannet.20150828.LAB.p12" \
  --pkpass="Q\!w2e3r4" \
  netlab1.dishcloth.com
```

## SSL Check

If you need to check a certificate that is in use:

```bash
openssl s_client \
  -connect meetups.dishcloth.com:443
```

## Extracting and Comparing Modulus Values with Private Keys

This extract the modulus and then runs an md5 hash on it, to make it a smaller bunch of text, so you can run `uniq` which is similar to a diff

```bash
(openssl x509 -noout -modulus -in meetupsdr.dishcloth.com.20160212.cer | openssl md5 ;openssl rsa -noout -modulus -in meetupsdr.dishcloth.com.20160212.key | openssl md5) | uniq
```

Successful results contains a single value, as there is only one value to compare.

## Combining certificates into a single PKCS12 file

```bash
sudo openssl pkcs12 \
  -export \
  -out int2lb101v.dishcloth.com.20140415.pfx \
  -inkey int2lb101v.dishcloth.com.20140415.key \
  -in ./int2lb101v.dishcloth.com.20140415.cer \
  -certfile dishclothissuingCA01_sept.cer \
  -certfile dishclothRootCA_sept.cer
```

You are done.

## Verify PKCS12

Issue this command, by example:

```bash
openssl pkcs12 \
  -info \
  -in cxwtest.dishcloth.com.20160425.pfx
```

## Checking certificate expiry en Masse

```bash
echo | openssl s_client -connect begw1.dishcloth.com:443 2>/dev/null | openssl x509 -noout -dates
```

## Extracting the Private key and Certificate, and Removing the Passphrase

### Export the private key

```bash
openssl pkcs12 \
  -in certname.pfx \
  -nocerts \
  -out key.pem \
  -nodes
```

### Export the certificate

```bash
openssl pkcs12 -in certname.pfx -nokeys -out cert.pem
```

### Remove the passphrase from the private key**

```bash
openssl rsa -in key.pem -out server.key
```

