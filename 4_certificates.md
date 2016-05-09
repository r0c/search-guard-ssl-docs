# Certificates



## Creating a Root CA

**WARNING: Danger Zone - If someone steal your root db files this would be a potential security flaw**

In case there is no PKI/Root CA established in you company don't worry. You can easily build your own.

1) Change the files under https://github.com/floragunncom/search-guard-ssl/tree/master/example-pki-scripts/etc to meet your needs. You should revise especially this section: 

<pre>
[ ca_dn ]
0.domainComponent       = "com"
1.domainComponent       = "example"
organizationName        = "Example Com Inc."
organizationalUnitName  = "Example Com Inc. Root CA"
commonName              = "Example Com Inc. Root CA"
</pre>

2) Install latest Open SSL Version and execute https://github.com/floragunncom/search-guard-ssl/blob/master/example-pki-scripts/gen_root_ca.sh like

<pre>
./gen_root_ca.sh capassword_use_a_strong_one truststorepassword
</pre>

Now you get a bunch of files and you you're now able to sign CSR's with you newly generated root CA

<pre>
openssl ca \
    -in thecsr.csr \
    -notext \
    -out signed-csr.pem \
    -config etc/signing-ca.conf \
    -extensions v3_req \
    -batch \
	-passin pass:capassword_use_a_strong_one \
	-extensions server_ext 
</pre>

Pls. see also https://github.com/floragunncom/search-guard-ssl/blob/master/example-pki-scripts/example.sh

## Creating a Root CA

For transport (node-to-code) encryption you need for every node a keystore and a truststore. The keystore contains the certificates which identifies this node to others. The truststore contains the certificates from the other nodes who should be trusted - this can be simplified by adding just the root certificate to the truststore. In this case every certificate which is a descendant of this root certificate is trusted. The keystore and the truststore may be the same file but can also be two different files/stores.

The following procedure assumes there is already a PKI (Public Key Infrastructure) in place - this means that a CSR (Certificate signing request) can be signed with a root certificate (so a Root CA exists). If you need to create your own Root CA pls refer to [this wiki article](https://github.com/floragunncom/search-guard-ssl/wiki/Create-your-own-Root-CA).

Pls. see also [example-pki-scripts](https://github.com/floragunncom/search-guard-ssl/blob/master/example-pki-scripts/example.sh)

1) Lets start with the truststore.
Get your root certificate chain (this means the root certificate itself and intermediate signing certificates if they exist) in PEM format and execute the following command:

<pre>
keytool  \
    -importcert \
    -file chain-ca.pem  \
    -keystore truststore.jks   \
    -storepass mysecret_ts_passwd  \
    -noprompt -alias root-ca
</pre>  

Distribute truststore.jks on all elasticsearch nodes and put it into the config/ folder

2) Generate for **each** node a **separate** keystore.

<pre>
#Generate a new key
keytool -genkey \
        -alias     NODE_NAME \
        -keystore  NODE_NAME-keystore.jks \
        -keyalg    RSA \
        -keysize   2048 \
        -validity  712 \
        -keypass mykspassword \
        -storepass mykspassword \
        -dname "CN=nodehostname.example.com, OU=department, O=company, L=localityName, C=US"

#Generate a CSR (Certificate signing request)
keytool -certreq \
        -alias      NODE_NAME \
        -keystore   NODE_NAME-keystore.jks \
        -file       NODE_NAME.csr \
        -keyalg     rsa \
        -keypass mykspassword \
        -storepass mykspassword \
        -dname "CN=nodehostname.example.com, OU=department, O=company, L=localityName, C=US"
</pre>

The important part here is **CN=nodehostname.example.com**
This has to be the full qualified hostname of your node or the ip address if no dns entry exists.

Send the NODE_NAME.csr to someone who can sign this with the root CA.

They send you back a file like NODE_NAME-signed.pem

<pre>
#Import the signed CSR together with the root certificate chain into the keystore

cat ca/chain-ca.pem NODE_NAME-signed.pem | keytool \
    -importcert \
    -keystore NODE_NAME-keystore.jks \
    -storepass mykspassword \
    -noprompt \
    -alias NODE_NAME
</pre>

Distribute NODE_NAME-keystore.jks to the appropriate node and put it into the config/ folder

Repeat for all nodes

Further reading
* See also [https://www.digitalocean.com/community/tutorials/java-keytool-essentials-working-with-java-keystores](https://www.digitalocean.com/community/tutorials/java-keytool-essentials-working-with-java-keystores)
* or [https://tomcat.apache.org/tomcat-8.0-doc/ssl-howto.html](https://tomcat.apache.org/tomcat-8.0-doc/ssl-howto.html)