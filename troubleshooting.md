# Troubleshooting

### Exception in thread "main" ElasticsearchException[No such keystore file ...]

```
Exception in thread "main" ElasticsearchException[No such keystore file /Users/jkressin/Development/elasticsearch-2.1.0/config/node-01-keystore.jks]
	at com.floragunn.searchguard.ssl.SearchGuardKeyStore.initSSLConfig(SearchGuardKeyStore.java:172)
	at com.floragunn.searchguard.ssl.SearchGuardKeyStore.<init>(SearchGuardKeyStore.java:129)
	at com.floragunn.searchguard.ssl.SearchGuardSSLModule.<init>(SearchGuardSSLModule.java:29)
	at com.floragunn.searchguard.ssl.SearchGuardSSLPlugin.nodeModules(SearchGuardSSLPlugin.java:89)
...
```

The path to the keystore file is incorrect. Please check the settings for the configuration key 

`searchguard.ssl.transport.keystore_filepath`

The value of this key is the path to the keystore file, **relative to the config directory**. For example, if you define `node-01-keystore.jks`, SG SSL expects the following file to exist:

`<es installation directory>/config/node-01-keystore.jks`

### Exception in thread "main" ElasticsearchException[No such truststore file ...]

```
Exception in thread "main" ElasticsearchException[No such truststore file /Users/jkressin/Development/elasticsearch-2.1.0/config/truststorea.jks]
	at com.floragunn.searchguard.ssl.SearchGuardKeyStore.initSSLConfig(SearchGuardKeyStore.java:182)
	at com.floragunn.searchguard.ssl.SearchGuardKeyStore.<init>(SearchGuardKeyStore.java:129)
	at com.floragunn.searchguard.ssl.SearchGuardSSLModule.<init>(SearchGuardSSLModule.java:29)
	at com.floragunn.searchguard.ssl.SearchGuardSSLPlugin.nodeModules(SearchGuardSSLPlugin.java:89)
```

The path to the truststore file is incorrect. Please check the settings for the configuration key 

`searchguard.ssl.transport.truststore_filepath`

The value of this key is the path to the truststore file, **relative to the config directory**. For example, if you define `truststore.jks`, SG SSL expects the following file to exist:

`<es installation directory>/config/truststore.jks` 

### java.io.IOException: DerInputStream.getLength(): lengthTag=..., too big.

```
Exception in thread "main" ElasticsearchException[DerInputStream.getLength(): lengthTag=109, too big.]; nested: IOException[DerInputStream.getLength(): lengthTag=109, too big.];
Likely root cause: java.io.IOException: DerInputStream.getLength(): lengthTag=109, too big.
	at sun.security.util.DerInputStream.getLength(DerInputStream.java:561)
	at sun.security.util.DerValue.init(DerValue.java:365)
	at sun.security.util.DerValue.<init>(DerValue.java:320)
	at sun.security.pkcs12.PKCS12KeyStore.engineLoad(PKCS12KeyStore.java:1872)
	at java.security.KeyStore.load(KeyStore.java:1433)
	at com.floragunn.searchguard.ssl.SearchGuardKeyStore.initSSLConfig(SearchGuardKeyStore.java:192)
	at com.floragunn.searchguard.ssl.SearchGuardKeyStore.<init>(SearchGuardKeyStore.java:129)
	at com.floragunn.searchguard.ssl.SearchGuardSSLModule.<init>(SearchGuardSSLModule.java:29)
```

SG SSL supports keystore/truststore files in either JKS or PKCS12 format. This exception means that the format you specified in the confoguration does not match the actual format of your keystore/truststore file. Please check the following configuration keys

* `searchguard.ssl.transport.keystore_type` 
* `searchguard.ssl.transport.truststore_type` 

Make sure it matches your keystore/truststore file format.

### java.security.UnrecoverableKeyException: Password verification failed

```
xception in thread "main" ElasticsearchException[Keystore was tampered with, or password was incorrect]; nested: IOException[Keystore was tampered with, or password was incorrect]; nested: UnrecoverableKeyException[Password verification failed];
Likely root cause: java.security.UnrecoverableKeyException: Password verification failed
	at sun.security.provider.JavaKeyStore.engineLoad(JavaKeyStore.java:770)
	at sun.security.provider.JavaKeyStore$JKS.engineLoad(JavaKeyStore.java:55)
	at java.security.KeyStore.load(KeyStore.java:1433)
	at com.floragunn.searchguard.ssl.SearchGuardKeyStore.initSSLConfig(SearchGuardKeyStore.java:192)
	at com.floragunn.searchguard.ssl.SearchGuardKeyStore.<init>(SearchGuardKeyStore.java:129)
	at com.floragunn.searchguard.ssl.SearchGuardSSLModule.<init>(SearchGuardSSLModule.java:29)
```

This simply means that the password for the keystore and/or truststore you provided in the configuration does not match the actual passord of your keystore/truststore. Check the following configuration entries to make sure the passwords are correct:

* `searchguard.ssl.transport.keystore_password` 
* `searchguard.ssl.transport.truststore_password`

### java.security.cert.CertificateException: No subject alternative names matching ... found

```
Caused by: java.security.cert.CertificateException: No subject alternative names matching IP address ... found
	at sun.security.util.HostnameChecker.matchIP(HostnameChecker.java:154)
	at sun.security.util.HostnameChecker.match(HostnameChecker.java:91)
	at sun.security.ssl.X509TrustManagerImpl.checkIdentity(X509TrustManagerImpl.java:455)
	at sun.security.ssl.X509TrustManagerImpl.checkIdentity(X509TrustManagerImpl.java:436)
	at sun.security.ssl.X509TrustManagerImpl.checkTrusted(X509TrustManagerImpl.java:252)
	at sun.security.ssl.X509TrustManagerImpl.checkServerTrusted(X509TrustManagerImpl.java:136)
	at sun.security.ssl.ClientHandshaker.serverCertificate(ClientHandshaker.java:1465)
```

This error is caused by a missing or invalid subject alternatice name (SAN) entry in the certificate, and only shows if hostname verification is enabled in the configuration (which is the default):

* `searchguard.ssl.transport.enforce_hostname_verification=true` 

If you enable hostname verification, make sure that the SAN entry in your certificate exists, and that it matches the host portion of the URL used to make the request. If you used the `example.sh` script to generate your certificates, the SAN already contains `127.0.0.1`. If you still get the error mentioned above, make sure that Elasticsearch is bound to that IP address in your `elasticsearch.yml` file:

* `network.host: 127.0.0.1`

Alternatively, you can disable hostname verification:

* `searchguard.ssl.transport.enforce_hostname_verification=false` 

**Note: by disabling hostname verification you expose your cluster to man-in-th-middle attacks. Please fix your certificates settings rather than disabling hostname verification**

### java.security.cert.CertificateException: No subject alternative DNS name matching ... found.

```
Caused by: java.security.cert.CertificateException: No subject alternative DNS name matching ... found.
	at sun.security.util.HostnameChecker.matchDNS(HostnameChecker.java:191)
	at sun.security.util.HostnameChecker.match(HostnameChecker.java:93)
	at sun.security.ssl.X509TrustManagerImpl.checkIdentity(X509TrustManagerImpl.java:455)
	at sun.security.ssl.X509TrustManagerImpl.checkIdentity(X509TrustManagerImpl.java:436)
	at sun.security.ssl.X509TrustManagerImpl.checkTrusted(X509TrustManagerImpl.java:252)
	at sun.security.ssl.X509TrustManagerImpl.checkServerTrusted(X509TrustManagerImpl.java:136)
	at sun.security.ssl.ClientHandshaker.serverCertificate(ClientHandshaker.java:1465)
```

If you enabled hostname verification in the configuration:

* `searchguard.ssl.transport.enforce_hostname_verification=true` 

and also enabled resolving the hostname against DNS:

* `searchguard.ssl.transport.resolve_hostname: true`

You must make sure that the hostname specified in the SAN field of your certificate can actually be resolved against DNS. 

Alternatively, you can disable the DNS lookup:

* `searchguard.ssl.transport.resolve_hostname: false`
