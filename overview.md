<!---
Copryight 2016 floragunn UG (haftungsbeschränkt)
-->

# Overview

Search Guard® SSL is a plugin for Elasticsearch which provides SSL/TLS encryption and authentication. It offers:

* Node-to-node encryption on the transport layer (SSL/TLS)
* Secure REST layer through HTTPS (SSL/TLS)
* JDK SSL and Open SSL support
* Support for Kibana 4, logstash and beats

The only external dependency is Netty 4 (and Tomcat Native if Open SSL is used).

Search Guard SSL is the foundation layer of Search Guard 2, which offers authentication and authorization on top of Search Guard SSL. Search Guard is also available as an Elasticsearch plugin from floragunn. You can find the github repository for Search Guard 2 [here](https://github.com/floragunncom/search-guard).

If you just need to encrypt your traffic, and make sure only trusted nodes and clients can join and access a cluster, Search Guard SSL is all you require. If you need authentication and authorization, you need both Search Guard SSL + Search Guard 2.

In this document the following abbreviations are being used:

* ES: Elasticsearch
* SG SSL: Search Guard SSL
* SG: Search Guard 2
 
This documentation refers only to Search Guard SSL 2.0 and above. Search Guard for ES 1.x is not actively maintained anymore.

## For the impatient

If you just want to get up and running quickly, please refer to the  [installation guide](installation.md) and the [quickstart tutorial](quick_start.md)

## Motivation

While Elasticsearch is often used for storing and searching sensitive data, it does not offer encryption or authentication/authorization out of the box. 

In order to secure your sensitive data, the first step is to encrypt the entire traffic from and to your ES cluster via TLS. While it is possible to implement an authentication/authorization plugin without TLS encryption, we strongly believe that this will only get you "fake security": An attacker can easily spy on your traffic, modify data and even join the cluster without permission. 

By using TLS the traffic between ES nodes and ES clients will be encrypted. Which means that

* you can be sure that nobody is spying on the traffic
* you can be sure that nobody tampered with the traffic

However by using SSL certificates you can also make sure that **only identified and trusted nodes or clients** can interact with the cluster.

To make this efficient SG SSL can use Open SSL as the SSL implementation.  

## TLS in a nutshell

**Note: We will explain how SG SSL uses TLS on the transport layer between nodes to encrypt the traffic, but it can also be used to encrypt the traffic on the REST layer. The principles are the same for both layers. While it is possible to use TLS on only one layer, you need to enable it for both transport and REST layer to get proper security.**

TLS (often referred to as "SSL" for historical reasons) is a cryptographic protocol to secure the communication over a computer network. It uses **symmetric encryption** to encrypt the transmitted data, by negotiating a **unique secret** at the start of any **TLS session** between two participating parties. It uses **public key cryptography** to authenticate the identity of the participating parties.

TLS supports many different ways for exchanging keys and encrypting data, but that is beyond the scope of this document. We'll focus only on the main concepts necessary to set up SG SSL.

### Certificates & Certificate authorities

TLS uses certificates ("digital certificates") for encrypting traffic and for identifying and authenticating the two participating parties in an TLS session. In a typical client/server scenario, only the identity of the server is verified. However, TLS is not limited to that, and we'll use client authentication later on in order to verify that only trusted clients can access ES.

To be more precise, 
> a digital certificate certifies the ownership of a public key by the named subject of the certificate. 
(Source: [Wikipedia](https://en.wikipedia.org/wiki/Transport_Layer_Security#Digital_certificates)).

The public key is then used for the actual encryption. 

#### Trust Hierarchies

A certificate can be used to issue and sign other certificates, building a trust hierarchy amongst these certificates. The certificates can be **intermediate root certificates** or **end entity certificates**.

A **root certificate**, issued by a **root certificate authority (root CA)** is at root of this hierarchy. A certificate authority represents a **trusted third party** authority, trusted both by the owner of the certificate and communication partner. All major browser come with a pre-installed list of trusted certificate authorities, like Thawte, GlobalSign, DigiCert or Comodo. 

A root CA uses its own certificate to issue other certificates. An intermediate root certificate can be used to issue other certifictes, while an end entity certificate is intended to be installed directly on a server directly. With an end entity certificate do you not sign other certificates.

If a TLS session is to be established, all certificates in the hierarchy are verified, up to the root certificate. The communication partner is only trusted if each certificate in this chain is trusted.

### SSL handshake

Before any user data is sent between these parties, first a **TLS session** has to be established. This process is called **TLS handshake**.   

The details of this handshake can vary slighty, depending on the selected cipher suite, but at a very high level the following steps are performed:

* The client sends a ClientHello message, containing the information which TLS version and cipher protocols it supports
* The server sends a ServerHello message, containng the choosen protocol version and cipher suite
* The server sends its certificate, containing the public key
* The client verifies the certificate and all intermediate certificates against its list of installed root CAs
* If the client cannot verify the certificate, it aborts the connection or displays a warning, depending on the type and the settings of the client 
* If the certificate could be verified, the client sends a "PreMasterSecret" to compute a common secret, called the "master secret". This master secret is used for encrypting the traffic. The PreMasterSecret is encrypted using the public key of the server certificate.
* The server tries to decrypt the clients message using its private key. If this fails, the handshake is considered as failed
* If decryption succeeds, the handshake is complete, and all traffic is encrypted using the master secret.

While at this point the servers identity is verified, the clients identity is not, since it never sent any information regarding its own identity. But this is possible too and it is called **Client Authentication** (aka **Mutual authentication** or **two-way authentication**) and is an optional part of the handshake protocol. If the server is configured to use client authentication, the following steps are added in the handshake:

* The server sends a CertificateRequest message to the client
* The client sends its own certificate, containing the clients public key, to the server
* The server verifies the certificate using its own installed list of root CAs and intermediate CA
* If thus succeeds, the connection is mutually authenticated

Note that this is a very simplified version of the handshake protocol, only to demonstrate the basic principles behind it. In short: The handshake protocol uses a combination of certificates and public/private keys to verify the identities of the communication partners and to negotiate a master secret for encrypting the traffic.

### Keystore and Truststore

Java uses **keystores** and a **truststores** to store certificates and private keys. The truststore contains all trusted certificates, typically root CAs and intermediate certificates. It is used to **verify credentials** from a communication partner.

The **keystore** holds the private keys and the associated certificates. It is used to **provide credentials** to the communication partner.

SG SSL supports two key-/truststore formats: JKS and PKCS12.

In a typical SG SSL setup, each node in the cluster has both a keystore and a truststore. If a node wants to communicate with another node, it uses its own certificate stored in the keystore to identify itself.

The other node uses the root CAs stored in the truststore to verify the other nodes certificate.

This of course is performed in both ways, because a node acts as a "client" and "server" at the same time: It sends requests to other nodes, acting as a client, and receives request, acting as a server.

## Minimal setup

A minimal SG SSL setup might look like this:

### Root CA

You need a root CA in order to create the required certificates for the nodes. If you already have a PKI infrastructure in place, you're good to go. Otherwise, you need to create a root CA first. SG SSL comes with scripts to help you set up your root CA.

### Generate certificates

Using the root CA, you then generate certificates for all participating nodes (and optionally clients, if you use client authentication) in your setup. These certificates are signed with your root certificate

### Generate keystore and truststore files

For each node, you create a keystore and a truststore file. The keystore contains the nodes own certificate, while the truststore contains the root CA. The truststore can be the same on all nodes, while the keystore should be different, since each node should have its own, exclusive certificate

### Configuring SG SSL

Add the configuration settings of SG SSL to the elasticsearch configuration, adjust it to your needs, and start the node.

**Note: Technically speaking, a Root CA is not really mandatory. You could as well just distribute the certificates of all nodes to the truststore of all nodes. While this would work, it's highly impractical. That is why we are not dig into this scenario any deeper.**
