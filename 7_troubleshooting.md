# Troubleshooting

## General configuration 

### Exception in thread "main" ElasticsearchException[No such keystore file ...]

### Exception in thread "main" ElasticsearchException[No such truststore file ...]

### java.io.IOException: DerInputStream.getLength(): lengthTag=..., too big.
-> Wrong format PKCS/JKS

### java.security.UnrecoverableKeyException: Password verification failed
-> Passwort keystore oder truststore falsch

### java.security.cert.CertificateException: No subject alternative DNS name matching localhost found.

-> Hostname verification


## OpenSSL

### java.lang.ClassNotFoundException: org.apache.tomcat.jni.SSL

* netty-tcnative jar is missing, see above
* make sure you use the netty-tcnative jar matching your platform, either linux-x86.jar or 64-fedora.jar or osx-x86_64.jar or windows-x86_64.jar

### java.lang.UnsatisfiedLinkError

* Openssl is not installed, see above
* Apache Portable Runtime (APR) is not installed, see above