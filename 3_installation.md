# Installing SG SLL

## Prerequisites

* Java 7 or 8 (Oracle Java 8 recommended)
* ElasticSearch 2.0 or above
* Optional: Tomcat Native and Open SSL

## Disable the security manager

If you running Elasticsearch 2.0.x or 2.1.x, then you have to disable the security manager in elasticsearch.yml. For Elasticsearch >= 2.2.x this is no longer necessary.

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


### Install the plugin

SG SLL can be installed it like any [other Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/plugins/2.2/plugin-management.html) plugin. Change to the installation directory of ES, and execute:

* bin/plugin install com.floragunn/search-guard-ssl/<version> OR
* sudo bin/plugin install com.floragunn/search-guard-ssl/<version>

Accept the following warning with y (since ES >= 2.2)

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
## Next steps

After performing these steps, SG SSL is installed. The next steps are

* Create and install the SSL certificates on each node
* Configure SG SSL
* Start your nodes
