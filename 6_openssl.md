# OpenSSL setup

Search Guard SSL can use Open SSL as the SSL implementation. This will result in better performance and better support for strong and modern cipher suites. With Open SSL it's also possible to use strong chipers without installing Java Cryptography Extension (JCE) Unlimited Strength Jurisdiction Policy Files. 

To enable native support for Open SSL follow these steps:

* Install latest [Open SSL version](https://www.openssl.org/community/binaries.html) on every node
* Install [Apache Portable Runtime](https://apr.apache.org) (libapr1) on every node
* Download netty-tcnative 1.1.33.Fork13 .jar for **your** platform http://repo1.maven.org/maven2/io/netty/netty-tcnative/1.1.33.Fork13/ (either _linux-x86.jar_ or _64-fedora.jar_ or _osx-x86_64.jar_ or _windows-x86_64.jar_ )
* Put it into the elasticsearch plugins/searchguard-ssl/ folder (on every node of course)
* If you update the plugin (or re-install it after removal) don't forget to add netty-tcnative .jar again
* Double check that you have enabled open ssl (which is the default):
<pre>
searchguard.ssl.transport.enable_openssl_if_available: true
searchguard.ssl.http.enable_openssl_if_available: true
</pre>
* You should see something like this when you starup elasticsearch:
<pre>
[INFO ][com.floragunn.searchguard.ssl.SearchGuardKeyStore] Open SSL OpenSSL 1.0.2d 9 Jul 2015 available
[INFO ][com.floragunn.searchguard.ssl.SearchGuardKeyStore] Open SSL available ciphers [ECDHE-RSA-AES256-GCM-SHA384,...
[INFO ][com.floragunn.searchguard.ssl.SearchGuardKeyStore] Open SSL ALPN supported tru
</pre>

If you face one of the following messages Open SSL is not available and Search Guard will use the built-in Java SSL implementation:
###java.lang.ClassNotFoundException: org.apache.tomcat.jni.SSL
* netty-tcnative jar is missing, see above
* make sure you use the netty-tcnative jar **matching your platform**, either _linux-x86.jar_ or _64-fedora.jar_ or _osx-x86_64.jar_ or _windows-x86_64.jar_ 

###java.lang.UnsatisfiedLinkError
* Openssl is not installed, see above
* Apache Portable Runtime (APR) is not installed, see above

###Further reading
* More about netty-tcnative can be found here: http://netty.io/wiki/forked-tomcat-native.html
* Apache Portable Runtime can be installed on Ubuntu with "sudo apt-get install libapr1"