---
description: >-
  Ockam Identities are unique, cryptographically verifiable digital identities.
  These identities authenticate by proving possession of secret keys. Ockam
  Vaults safely store these secret keys.
---

# Identities and Vaults

In order to make decisions about trust, we must authenticate senders of messages.

## Vaults

Ockam [Identities](identities.md#identity) authenticate by cryptographically proving possession of specific secret keys.  Ockam Vaults safely store these secret keys in cryptographic hardware and cloud key management systems.

You can create a vault as follows:&#x20;

```
» ockam vault create v1
     ✔︎ Vault created with name 'v1'!
```

This command will, by default, create a file system based vault, where your secret keys are stored at a specific file path.

Vaults are designed to be used in a way that secret keys never have to leave a vault. There is a growing base of Ockam Vault implementations in the [<mark style="color:blue;">Ockam GitHub Repository</mark>](https://github.com/build-trust/ockam) that safely store secret keys in specific KMSs, HSMs, Secure Enclaves etc.

## Identities

Ockam Identities are unique, cryptographically verifiable digital identities.

You can create new identities, by typing: &#x20;

```
» ockam identity create i1 --vault v1
     ✔︎ Identity Pef7f2a20c186b5adb03c0d7160879134135574663cc930d9b1cd664d63a45fb0
       created successfully as i1
```

The secret keys belonging to this identity are stored in the specified vault. This can be any type of vault - File Vault, AWS KMS, Azure KeyVault, YubiKey etc. If no vault is specified, the default vault is used. If a default vault doesn't exist yet, a new file systems based vault is created, set as default, and then used to generate secret keys.

To ensure privacy and eliminate the possibility of correlation of behavior across trust contexts, we've made it easy to generate and use different identities and identifiers for separate trust contexts.

### Secret Keys

Each Ockam Identity starts its life by generating a secret key and its corresponding public key. Secret keys, must remain secret, while public keys can be shared with the world.

Ockam Identities support two types of Elliptic Curve secret keys that live in vaults - Curve25519 or NIST P-256.

### Identifiers

Each Ockam Identity has a unique public identifier, called the <mark style="color:orange;">Ockam Identifier</mark> of this identity:

```
» ockam identity show i1
Pef7f2a20c186b5adb03c0d7160879134135574663cc930d9b1cd664d63a45fb0
```

This Identifier is generated by hashing the first public key of the Identity.

### Change History

Ockam Identities can periodically rotate their keys to indicate that the latest public key is the one that should be used for authentication. Each Ockam Identity maintains a self-signed change history of key rotation events, you can see this full history by running:

```
» ockam identity show i1 --full
Change History:
  Change[0]:
    identifier: 05c9d623927bdde6747c83df0530e61e9031fcf1c79ddf767578cfc424fc2940
    change:
      prev_change_identifier: 0547c93239ba3d818ec26c9cdadd2a35cbdf1fa3b6d1a731e06164b1079fb7b8
      label:        OCKAM_RK
      public_key:   Ed25519 4998eca83ded23e336c0fc74cb512dce7e127dff901204dbdaa148c63e7fa020
    signatures:
      [0]: SelfSign 387b657c94a6522937159110866c34f230337a07803703ae86cb14aa1afb81a7bb760cb5b0850cd1074e45a0cf40beef3ba5e59114846ad91dcc2bafb7e05a06
```

### Identifier Authentication

Authentication, within Ockam, starts by proving control of a specific Ockam Identifier. To prove control of a specific Identifier, the prover must present the identifier, the full signed change history of the identifier, and a signature on a challenge using the secret key corresponding to the latest public key in the identifier's change history.

{% hint style="info" %}
If you're stuck or have questions at any point, [<mark style="color:blue;">please reach out to us</mark>](https://www.ockam.io/contact)<mark style="color:blue;">**.**</mark>
{% endhint %}

Next, let's combine everything we've learnt so far to create mutually authenticated and end-to-end encrypted [<mark style="color:blue;">secure channels</mark>](secure-channels.md) that guarantee data authenticity, integrity, and confidentiality.