# PKCP - Public-Key Chaining Protocol

Bitcoin-based public-key chaining protocol for self-sovereign digital identity management. Each person using any system that relies on public-private key pairs to uniquely identify users can publish the public keys as inscriptions on a satoshi and thus link them into one identity.

No issuer, no certificate authority, no personal details. Your keys are in your hands.

## Specification

The Bitcoin blockchain is used as a distributed public-key store.

An identity is a chain of consecutive public keys inscribed on the same satoshi. The inscribed satoshi is the cornerstone of the identity, a cornersat. 

The Ordinals Protocol is used to inscribe the satoshi, read the inscription content, and keep track of the cornerstone satoshi by its ordinal number or name, ref.  https://docs.ordinals.com/inscriptions.html#inscriptions .

The controller is the entity in charge of the identity and can be an individual or an organization. 


### Publish and rotate keys

To publish an identity, you create an inscription on a satoshi. Inscriptions are permanent and immutable. Each satoshi can be inscribed multiple times, ref. https://docs.ordinals.com/inscriptions.html#reinscriptions . 
The inscription must contain plain text content, where the content is a string, containing the identity’s public key in the format:
```
pkcp:<public key>
```

To rotate the certificate, a new key pair is created, and the latest public key is inscribed on the same satoshi, using the same syntax as above.

When a new inscription is added, the previous key becomes read-only. No new records can be created with the previous, read-only certificate. 
This means that all records created with any previous key can still be validated and they all contribute to the history of the identity. 


### Deprecate

To permanently deprecate an identity, publish an inscription in the format:
```
pkcp:d
```

A grace period of 48 hrs is granted to re-enable the identity with a new public key. If the grace period is exceeded, any further keys are ignored.

