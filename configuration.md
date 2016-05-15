# Configuration

All configuration settings for SG SSL need to be placed in `config/elasticsearch.yml`. The SG SSL repository contains a configuration template (`searchguard-ssl-config-template.yml`) with all configuration options available. It's located at the root of the repository.

## Transport layer SSL                                                                       

### Enabling and disabling transport layer SSL

**searchguard.ssl.transport.enabled: (true/false)**

Enable or disable node-to-node ssl encryption. (Optional, default: true)

### Keystore

**searchguard.ssl.transport.keystore_type: (PKCS12/JKS)**

The type of the keystore file, JKS or PKCS12 (Optional, default: JKS)

**searchguard.ssl.transport.keystore_filepath: keystore_node1.jks**

Path to the keystore file, relative to the `config/` directory (mandatory)

**searchguard.ssl.transport.keystore_alias: my_alias**

Alias name (Optional, default: first alias which could be found)

**searchguard.ssl.transport.keystore_password: changeit**

Keystore password (default: changeit)

### Truststore

**searchguard.ssl.transport.truststore_type: PKCS12**

The type of the truststore file, JKS or PKCS12 (default: JKS)

**searchguard.ssl.transport.truststore_filepath: truststore.jks**

Path to the truststore file, relative to the `config/` directory (mandatory)

**searchguard.ssl.transport.truststore_alias: my_alias**

Alias name (default: first alias which could be found)

**searchguard.ssl.transport.truststore_password: changeit**

Truststore password (default: changeit)

### Hostname verification

**searchguard.ssl.transport.enforce_hostname_verification: true**

Enforce hostname verification (default: true). 

**searchguard.ssl.transport.resolve_hostname: true**

If hostname verification is enabled, specify whether the hostname should be resolved against DNS (default: true).

### Open SSL

**searchguard.ssl.transport.enable_openssl_if_available: false**

Use native Open SSL instead of JDK SSL if available (default: true)

## HTTP/REST layer SSL                                                                       

### Enabling and disabling HTTP/REST layer SSL

**searchguard.ssl.http.enabled: true**

Enable or disable REST layer security (https), (default: false)

### Keystore

**searchguard.ssl.http.keystore_type: PKCS12**

The type of the keystore file JKS or PKCS12 (default: JKS)

**searchguard.ssl.http.keystore_filepath: keystore_https_node1.jks**

Path to the keystore file, relative to the `config/` directory (mandatory)

**searchguard.ssl.http.keystore_alias: my_alias**

Alias name (default: first alias which could be found)

**searchguard.ssl.http.keystore_password: changeit**

Keystore password (default: changeit)

### Truststore

**searchguard.ssl.http.truststore_type: PKCS12**

The type of the truststore file JKS or PKCS12 (default: JKS)

**searchguard.ssl.http.truststore_filepath: truststore_https.jks**

Path to the truststore file, relative to the `config/` directory (mandatory)

**searchguard.ssl.http.truststore_alias: my_alias**

Alias name (default: first alias which could be found)

**searchguard.ssl.http.truststore_password: changeit**

Truststore password (default: changeit)

### OpenSSL

**searchguard.ssl.http.enable_openssl_if_available: false**

Use native Open SSL instead of JDK SSL if available (default: true)

### Client authentication

**searchguard.ssl.http.clientauth_mode: REQUIRE**

Do the clients (typically the browser or the proxy) have to authenticate themself to the http server? To enforce authentication use REQUIRE, to completely disable client certificates use NONE, to use authentication when a certificate is availabl on the browser, use OPTIONAL. Default is OPTIONAL.
