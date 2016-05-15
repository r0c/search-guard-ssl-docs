# Installing SG SLL

## Prerequisites

* Java 7 or 8 (Oracle Java 8 recommended)
* Elasticsearch 2.0 or above
* Optional: Tomcat Native and Open SSL

## Disable the security manager

If you are running Elasticsearch 2.0.x or 2.1.x, then you have to disable the security manager in elasticsearch.yml. For Elasticsearch >= 2.2.x this is no longer necessary.

In order to disable the security manager, add the following line to your elastcsearch.yml configuration file:

```
security.manager.enabled=false
```

## Install SG SSL

### Choosing the right version

The versioning schema of SG SLL is: e1.e2.e3.sgv

where:

* e1: Elasticsearch Major Version
* e2: Elasticsearch Minor Version
* e2: Elasticsearch Fix Version
* sgv: Search Guard SSL Version

The following table illustrated which SG SSL version you need for a particular ES version:

| ES version        | SG SSL version |
| ------------- |:-------------:| 
| 1.x.y      | not supported |
| 2.0      | not supported      |
| 2.0.1 | not supported      |
| 2.0.2 | 2.0.2.5      |
| 2.1.0 | 2.1.0.5      |
| 2.1.1 | 2.1.1.5      |
| 2.1.2 | 2.1.2.5      |
| 2.2.0 | 2.2.0.6      |
|2.2.1	|2.2.1.7 |
|2.3.1	|2.3.1.8|
|2.3.2	|2.3.2.9|

For an up-to-date version matrix, you can always refer to the [Wiki on github](https://github.com/floragunncom/search-guard-ssl/wiki)


### Install the plugin

Before installing the plugin, stop your ES node(s) if necessary.

#### From Maven

SG SLL can be installed it like any [other Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/plugins/2.2/plugin-management.html) plugin. Change to the installation directory of ES, and execute:

* bin/plugin install com.floragunn/search-guard-ssl/<version> OR
* sudo bin/plugin install com.floragunn/search-guard-ssl/<version>

"&lt;version&gt;" is for example 2.3.1.8 (NOT v2.3.1.8)

Accept the following warning message by typing "y" (since ES >= 2.2)

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@     WARNING: plugin requires additional permissions     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
* java.lang.RuntimePermission accessClassInPackage.sun.misc
* java.lang.RuntimePermission getClassLoader
* java.lang.RuntimePermission loadLibrary.*
* java.lang.reflect.ReflectPermission suppressAccessChecks
* java.security.SecurityPermission getProperty.ssl.KeyManagerFactory.algorithm
See http://docs.oracle.com/javase/8/docs/technotes/guides/security/permissions.html
for descriptions of what these permissions allow and the associated risks.
```

#### From github

Navigate to the SG SSL github repository ([https://github.com/floragunncom/search-guard-ssl](https://github.com/floragunncom/search-guard-ssl)), and choose the version to download:

![Download SG as zip archive](images/choose_version_github.jpg)

Albeit optional, we strongly recommend to verify the integrity of the downloaded zip file. We provide PGP signatures for every release file. This signature should be matched against the KEYS file contained in the downloaded zip file. We also provide MD5 and SHA-1 checksums for every release file. After you download the zip, you should calculate a checksum for your download, and make sure it is the same as ours. For a how-to on verifying pgp signatures, please look here:

* [http://www.openoffice.org/download/checksums.html](http://www.openoffice.org/download/checksums.html)
* [https://www.apache.org/info/verification.html](https://www.apache.org/info/verification.html)

After the integrity was verified, navigate to the installation directory of ES and type:

```
bin/plugin -u file:///path/to/search-guard-<version>.zip -i search-guard
```

## Next steps

After performing these steps, SG SSL is installed. Next:

* Create and install the SSL certificates on each node
 * see chapters ["Quickstart"](quick_start.md) and ["Certificates"](certificates.md)
* Configure SG SSL
 * see chapter ["Configuration"](configuration.md) 
* Start your nodes and enjoy the added security