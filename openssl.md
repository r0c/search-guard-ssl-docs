# OpenSSL setup

Search Guard SSL can use Open SSL as the SSL implementation. This will result in better performance and better support for strong and modern cipher suites. With Open SSL its also possible to use strong chipers without installing Java Cryptography Extension (JCE) Unlimited Strength Jurisdiction Policy Files. 

To enable native support for Open SSL follow these steps:

* Install latest OpenSSL version on every node (make sure its at least version 1.0.1k.)
 *  [https://www.openssl.org/community/binaries.html](https://www.openssl.org/community/binaries.html)
* Install Apache Portable Runtime (libapr1) on every node
 * [https://apr.apache.org](https://apr.apache.org)
 * On Ubuntu, Apache Portable Runtime can be installed with `sudo apt-get install libapr1`
* Download netty-tcnative 1.1.33.Fork15 .jar for **your** platform 
 * http://repo1.maven.org/maven2/io/netty/netty-tcnative/1.1.33.Fork15/version, where version is one of `_linux-x86.jar_`, `_64-fedora.jar_`, `_osx-x86_64.jar_`
 or `_windows-x86_64.jar_`
* Put it into the elasticsearch `plugins/searchguard-ssl/` folder (on every node of course)
* If you update the plugin (or re-install it after removal) don't forget to add netty-tcnative .jar again
* Check that you have enabled OpenSSL in the configuration
 * `searchguard.ssl.transport.enable_openssl_if_available: true`
 * `searchguard.ssl.http.enable_openssl_if_available: true`

If you did all the steps above and start your nodes, you should see an entry similar to this in the logfile:

```
[INFO ][com.floragunn.searchguard.ssl.SearchGuardKeyStore] Open SSL OpenSSL 1.0.2d 9 Jul 2015 available
[INFO ][com.floragunn.searchguard.ssl.SearchGuardKeyStore] Open SSL available ciphers [ECDHE-RSA-AES256-GCM-SHA384,...
```

If you face one of the following messages OpenSSL is not available and Search Guard SSL will use the built-in Java SSL implementation:

###java.lang.ClassNotFoundException: org.apache.tomcat.jni.SSL
* netty-tcnative jar is missing, see above
* make sure you use the netty-tcnative jar **matching your platform**, either `_linux-x86.jar_` or `_64-fedora.jar_` or `_osx-x86_64.jar_` or `_windows-x86_64.jar_` 

###java.lang.UnsatisfiedLinkError
* OpenSSL is not installed, see above
* Apache Portable Runtime (APR) is not installed, see above

###Further reading
* More about netty-tcnative can be found here: 
 * [http://netty.io/wiki/forked-tomcat-native.html](http://netty.io/wiki/forked-tomcat-native.html)