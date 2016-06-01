# Certificates

As described in the [quickstart tutorial](quickstart.md), you can always use our `example.sh` script to generate a root CA and all required certificates. The `example.sh` basically just executes three other scripts:

* `gen_root_ca.sh`
 * Generates the root CA
* `gen_node_cert.sh`
 * Generates a certificate for one node
* `gen_client_node_cert.sh`
 * Generates a client certificate

Depending on your needs, you can of course execute each script manually, or use OpenSSL commands directly. If you already have a PKI infrastructure in place, please ask the respective authority in your company to create the required assets.

In case there is no PKI/Root CA established in your company yet, don't worry. You can easily build your own as described below.
 
**The following section asumes you have OpenSSL installed on your machine. Execute all commands from inside the `example-pki-scripts` folder.**

## Creating a Root CA

The configuration settings for the Root CA are located in the `example-pki-scripts/etc` folder. You need to adjust two files:

* `root-ca.conf`
* `signing-ca.conf`

You should revise especially this section: 

```
[ ca_dn ]
0.domainComponent       = "com"
1.domainComponent       = "example"
organizationName        = "Example Com Inc."
organizationalUnitName  = "Example Com Inc. Root CA"
commonName              = "Example Com Inc. Root CA"
```

And change the example values according to your needs.

In order to generate the root CA, execute the script `example-pki-scripts/gen_root_ca.sh` like:

```
./gen_root_ca.sh capassword_use_a_strong_one truststorepassword
```

The script expects two paramaters, the CA password and the truststore password. 

**If you're planning to use the generated artifacts for production, make sure to use strong passwords!**

Now you get a bunch of files and you're now able to sign certificate signing requests (CSR) with you newly generated root CA. In other words, you can now start generating certificates for each node, and sign them with your root CA. 

## Creating the truststore

If you used the `example-pki-scripts/gen_root_ca.sh`, it already created a ready-to-use truststore file for you. 

If you created the root certificate (chain) some other way, or if your company already has a root certificate (chain) in place, you can create the truststore manually. Get your root certificate chain (this means the root certificate itself and intermediate signing certificates if they exist) in PEM format and execute the following command:

```
keytool  \
    -importcert \
    -file chain-ca.pem  \
    -keystore truststore.jks   \
    -storepass mysecret_ts_passwd  \
    -noprompt -alias root-ca
```

In both cases, distribute the generated `truststore.jks` file to the `config` directory of all participating nodes of your cluster.

## Creating and signing CSRs

A certificate signing request is a block of encrypted text, usually, but not always, generated on the server where you plan to use the certificate. It contains information about your organization, and the public key to be used with the certificate. 

If you want to obtain a certificate you can use on a server, or, in this case, on a ES node, you:

* create a CSR
* send the CSR to a root CA
* get back a signed certificate

This needs to be done for each node separately.

For a tutorial how to create CSRs, please see here: [https://www.digicert.com/csr-creation.htm](https://www.digicert.com/csr-creation.htm). 

You need to create a CSR for each node in your cluser. In the following "NODE_NAME" stands for the name of your ES node. You can pick any name you like here, for example `mycompany-elk-node-1`.

If you are familiar with OpenSSL you can use the following command directly to generate a CSR and private key:

```
openssl req -new -keyout NODE_NAME.key -out NODE_NAME.csr
```
You can also use Javas keytool to generate keys and CSRs:

Generate a new key:

```
keytool -genkey \
        -alias     NODE_NAME \
        -keystore  NODE_NAME-keystore.jks \
        -keyalg    RSA \
        -keysize   2048 \
        -sigalg SHA256withRSA \
        -validity  712 \
        -keypass mykspassword \
        -storepass mykspassword \
        -dname "CN=nodehostname.example.com, OU=department, O=company, L=localityName, C=US" \
        -ext san=dns:nodehostname.example.com 
```

Generate a CSR (Certificate signing request):
```
keytool -certreq \
        -alias      NODE_NAME \
        -keystore   NODE_NAME-keystore.jks \
        -file       NODE_NAME.csr \
        -keyalg     rsa \
        -keypass mykspassword \
        -storepass mykspassword \
        -dname "CN=nodehostname.example.com, OU=department, O=company, L=localityName, C=US" \
        -ext san=dns:nodehostname.example.com 
```
The important part here is CN=nodehostname.example.com This has to be the full qualified hostname of your node, or the ip address if no DNS entry exists.

You can now sign it with your root CA by using the following command:

```
openssl ca \
    -in NODE_NAME.csr \
    -notext \
    -out signed-NODE_NAME.csr.pem \
    -config etc/signing-ca.conf \
    -extensions v3_req \
    -batch \
	-passin pass:capassword_use_a_strong_one \
	-extensions server_ext 
```

Alternatively, send the CSR to someone in your organization who can sign it with the root CA.

## Creating the keystore

You can now import the signed certificates along with the intermediate certificates (if any) to the keystore. 

```
cat ca/chain-ca.pem NODE_NAME-signed.pem | keytool \
    -importcert \
    -keystore NODE_NAME-keystore.jks \
    -storepass mykspassword \
    -noprompt \
    -alias NODE_NAME
```

Distribute `NODE_NAME-keystore.jks` to the appropriate node and put it into the config/ folder

## Further reading

* [https://www.digitalocean.com/community/tutorials/java-keytool-essentials-working-with-java-keystores](https://www.digitalocean.com/community/tutorials/java-keytool-essentials-working-with-java-keystores)
* [https://tomcat.apache.org/tomcat-8.0-doc/ssl-howto.html](https://tomcat.apache.org/tomcat-8.0-doc/ssl-howto.html)