# PKCP - Public-Key Chaining Protocol

Bitcoin-based public-key chaining protocol for self-sovereign digital identity management. Anyone using any system that relies on public-private key pairs to uniquely identify users can publish the public keys as inscriptions on a satoshi and thus link them into one identity.

No issuer, no certificate authority, no personal details. Your keys are in your hands.


## Specification

The Bitcoin blockchain is used as a distributed public-key store.

A certificate is a public-private key pair.

An identity is a chain of consecutive public keys inscribed on the same satoshi. The inscribed satoshi is the cornerstone of the identity, a cornersat. 

The [Ordinals Protocol](https://docs.ordinals.com/) is used to inscribe the satoshi, read the inscription content, and keep track of the cornerstone satoshi by its ordinal number or name. Inscriptions are permanent and immutable. This protocol leverages the mostly ignored fact that each satoshi can be [inscribed multiple times](https://docs.ordinals.com/inscriptions.html#reinscriptions).

The controller is the entity in charge of the identity and can be an individual or an organization. The controller holds the keys of a non-empty Bitcoin wallet.


### Publish and rotate keys

To publish an identity, the controller first creates a certificate and then an inscription on a satoshi. 
The inscription must contain plain text content, where the content is a string, containing the identity’s public key in the format:
```
pkcp:<public key>
```

To rotate the certificate, the controller creates a new key pair, and then inscribes the latest public key on the same satoshi using the same syntax as above.

When a new inscription is added, the previous certificate becomes read-only. No new records can be created with the previous, read-only certificate. 
This means that all records created with any previous key can still be validated and they all contribute to the history of the identity. 

The certificates have no predefined expiration time.


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

Since all names are comprised of only letters, dots can be used as spacers for better legibility, but are ignored otherwise.

Identity management and trading are entirely in the hands of the identity controller.


## Security considerations

It is recommended that the controller is in fact the owner of the cornerstone satoshi. The controller needs to own the satoshi to inscribe it but then needs to take care to not sell it inadvertently.

Any services interacting with these identities need to make sure that the controller provides proof of the corresponding private key. 


