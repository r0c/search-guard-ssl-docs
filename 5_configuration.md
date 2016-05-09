# Configuration


## Transport layer SSL                                                                       

### Enabling and disabling transport layer SSL

**searchguard.ssl.transport.enabled: (true/false)**

Enable or disable node-to-node ssl encryption. (Optional, default: true)

### Keystore

**searchguard.ssl.transport.keystore_type: (PKCS12/JKS)**

JKS or PKCS12 (Optional, default: JKS)

**searchguard.ssl.transport.keystore_filepath: keystore_node1.jks**

Relative path to the keystore file (mMndatory, this stores the server certificates), must be placed under the config/ dir

**searchguard.ssl.transport.keystore_alias: my_alias**

Alias name (Optional, default: first alias which could be found)

**searchguard.ssl.transport.keystore_password: changeit**

Keystore password (default: changeit)

### Truststore

**searchguard.ssl.transport.truststore_type: PKCS12**

JKS or PKCS12 (default: JKS)

**searchguard.ssl.transport.truststore_filepath: truststore.jks**

Relative path to the truststore file (mandatory, this stores the client/root certificates), must be placed under the config/ dir

**searchguard.ssl.transport.truststore_alias: my_alias**

Alias name (default: first alias which could be found)

**searchguard.ssl.transport.truststore_password: changeit**

Truststore password (default: changeit)

### Hostname verification

**searchguard.ssl.transport.enforce_hostname_verification: true**

Enforce hostname verification (default: true)

**searchguard.ssl.transport.resolve_hostname: true**
If hostname verification specify if hostname should be resolved (default: true)

### Open SSL

**searchguard.ssl.transport.enable_openssl_if_available: false**

Use native Open SSL instead of JDK SSL if available (default: true)

## HTTP/REST layer SSL                                                                       

### Enabling and disabling HTTP/REST layer SSL

**searchguard.ssl.http.enabled: true**

Enable or disable rest layer security - https, (default: false)

### Keystore

**searchguard.ssl.http.keystore_type: PKCS12**

JKS or PKCS12 (default: JKS)

**searchguard.ssl.http.keystore_filepath: keystore_https_node1.jks**

Relative path to the keystore file (this stores the server certificates), must be placed under the config/ dir

**searchguard.ssl.http.keystore_alias: my_alias**

Alias name (default: first alias which could be found)

**searchguard.ssl.http.keystore_password: changeit**

Keystore password (default: changeit)

### Truststore

**searchguard.ssl.http.truststore_type: PKCS12**

JKS or PKCS12 (default: JKS)

**searchguard.ssl.http.truststore_filepath: truststore_https.jks**

Relative path to the truststore file (this stores the client certificates), must be placed under the config/ dir

**searchguard.ssl.http.truststore_alias: my_alias**

Alias name (default: first alias which could be found)

**searchguard.ssl.http.truststore_password: changeit**

Truststore password (default: changeit)

### OpenSSL

**searchguard.ssl.http.enable_openssl_if_available: false**

Use native Open SSL instead of JDK SSL if available (default: true)

### Client authentication

**searchguard.ssl.http.enforce_clientauth: false**

Do the clients (typically the browser or the proxy) have to authenticate themself to the http server, default is false
