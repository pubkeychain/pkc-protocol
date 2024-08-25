# PKCP - Public-Key Chaining Protocol

Bitcoin-based public-key chaining protocol for self-sovereign digital identity management. Anyone using any system that relies on public-private key pairs to uniquely identify users can publish the public keys as inscriptions on a satoshi and thus link them into one identity.

No issuer, no certificate authority, no personal details. Your keys are in your hands.


## Specification

The Bitcoin blockchain is used as a distributed public-key store.

A certificate is a public-private key pair.

An identity is a chain of consecutive public keys inscribed on the same satoshi. The inscribed satoshi is the cornerstone of the identity, a cornersat. 

The [Ordinal Theory](https://docs.ordinals.com/) is used to inscribe the satoshi, read the inscription content, and keep track of the cornerstone satoshi by its ordinal number or name. Inscriptions are permanent and immutable. This protocol leverages the fact that each satoshi can be [inscribed multiple times](https://docs.ordinals.com/inscriptions.html#reinscriptions).

The controller is the entity in charge of the identity and can be an individual or an organization. The controller holds the keys of a non-empty Bitcoin wallet.


### Publish and rotate keys

To publish an identity, the controller first creates a certificate and then an inscription on a satoshi. 
The inscription must contain plain text content, where the content is a string, containing the identity’s public key in the format:
```
pkcp:<public key>
```

The controller creates a new key pair to rotate the certificate, then inscribes the latest public key on the same satoshi using the same syntax as above.

When a new inscription is added, the previous certificate becomes read-only. No new records can be created with the prior, read-only certificate. 
This means that all records created with any previous key can still be validated and they all contribute to the history of the identity. 

The certificates have no predefined expiration time.

For details on key rotation for nostr see [NIP-alpaca](https://github.com/pubkeychain/nips/blob/main/alpaca.md).

#### Website resolver

The satoshi name can also be used as a static identifier for a website.

To publish a website, the controller first creates a certificate for the website and then an inscription on a satoshi, where the current IP address is added at the end of the inscription:
```
pkcp:<public key>:<server IP>
```

The name of the satoshi can be used to look up the public key and the current IP at once. The server is expected to use the corresponding private key in its communication. This model skips the CA and the DNS resolving procedures.

The IP addresses and the keys can be rotated independently.

The satoshi names are mostly random collections of letters. This means they function more like telephone numbers than domain names we are used to. Since we access most websites by following shared links, ads, QR codes, and clickable search results, there is no reason why the same could not be implemented using the satoshi names as a static identifier without relying on readability.


### Deprecate

To permanently deprecate an identity, publish an inscription in the format:
```
pkcp:d
```

A grace period of 48 hrs is granted to re-enable the identity with a new public key.

Any keys inscribed after the identity was irrevocably deprecated constitute a new and unrelated identity with no claim to the previously inscribed keys.


### Decentralized trustless naming solution

Once the controller creates an identity by inscribing a satoshi, they are automatically granted the use of the namespace derived by base26 encoding the cornerstone satoshi ordinal number (satoshi name). 

Since satoshis are numbered consecutively, this automatically makes all the names unique and no third party needs to take care of the naming conflicts. 

Since all names are comprised of only letters, dots can be used as spacers for better legibility but are ignored otherwise.

Identity management and trading are entirely in the hands of the identity controller.


## Security and network considerations

It is recommended that the controller owns the cornerstone satoshi. The controller needs to own the satoshi to inscribe it but then needs to take care to not sell it inadvertently.

Any services interacting with these identities need to make sure that the controller provides proof of the corresponding private key. No service built on this protocol should take the complete list of public keys at face value. Bad actors can easily engineer a public key inscription but not the corresponding private key. History cannot be changed and past certificates cannot retroactively be claimed. For the services, history starts at the first interaction and only a currently valid certificate should be considered. The services should also require proof of current/latest certificate for further interactions. Public keys with which no valid interactions were recorded should be ignored even if they fall between two validated keys on the same satoshi. 

This protocol does not actively safeguard from impostors. Use individually trusted sources as independent proof (based on web-of-trust principles).   

No changes need to be implemented on the Bitcoin network. The protocol is lightweight. The proposed inscriptions are short and plain text. Ordinal Explorer provides a sufficient API to keep track of inscribed satoshis.


