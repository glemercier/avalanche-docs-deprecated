# Glossary

## Address

An address is used to identify private key holders when transferring value. Most addressing systems follow this pattern;

* Users control private keys.
* Addresses are used to identify private key owners without exposing the private key itself. 
* Addresses are derived from public keys.
* Using a signature signed from the private key and the message the private key signed, the public key can be recovered. 
* The public key is then used to generate the address.
* The address generation function changes from VM to VM.

The Avalanche platform is not prescriptive about addressing schemes. Each VM may select its own addressing scheme. Default chains on the Avalanche platfrom use the following addressing schemes: 

The Avlanche C-Chain follows the EVM addressing system. It has a secp256k1 public/private keypair. Using the 64 byte address, a hashing function called [keccak256](https://eth-hash.readthedocs.io/en/latest/) is applied to the byte array. The resulting hash is truncated to the first 20 bytes. The address string is represented as a hexidecimal value, always prefixed with "0x".   

The Avalanche X-Chain and P-Chain use a binary 20 byte array for the raw address. It is created as follows:

* Obtain the 33-byte public key of the Secp256k1 keypair
* This public key is hashed once using SHA256.
* The resultant hash is then hashed again using RIPEMD160, producing a byte array of length 20. 

This byte array is the address. Additional information about the raw-binary implementation of addressing on the Primary Network can be found in [cryptographic primitives](../cryptographic-primitives/#secp256k1-addresses). 

There are conventions in the Avalanche platform for distributing readable addresses. All addresses are [Bech32](#bech32) encoded. By convention the blockchainID for the address is prepended to the address in the format `chainID-address`. Aliases for chainID may be used as well. Example:

```
/* P-Chain address */
P-avax1am4w6hfrvmh3akduzkjthrtgtqafalce6an8cr

/* C-Chain address */
C-0x820891f8b95daf5ea7d7ce7667e6bba2dd5c5594

/* X-Chain address */
X-avax1kj06lhgx84h39snsljcey3tpc046ze68mek3g5

/* Same address on a different chain */
P-avax1kj06lhgx84h39snsljcey3tpc046ze68mek3g5

/* Same address, using the chainID instead of the chain alias */
11111111111111111111111111111111LpoYY-avax1kj06lhgx84h39snsljcey3tpc046ze68mek3g5
```

In our Bech32 standard, there are pre-defined HRPs for our addresses:

* NetworkID 1: avax
* NetworkID 2: cascade
* NetworkID 3: denali
* NetworkID 4: everest
* NetworkID 5: fuji
* NetworkID 12345: local
* Other NetworkIDs: custom

## AVAX

AVAX is the Avalanche network's native asset. It has two special uses on the Avalanche network:

* Validators must [stake](../core-concepts/overview.md#what-is-staking) AVAX
* Transaction fees will be paid in AVAX

## [Avalanche Consensus](../core-concepts/overview.md#what-is-avalanche)

Avalanche is a family of consensus protocols used by blockchains on the Avalanche network.
Avalanche-based protocols allow computers to agree on a large set of information very quickly and with high security.

## Avalanche Virtual Machine

The Avalanche Virtual Machine is one of the Avalanche network's built-in virtual machines. 
It defines an application for creating and trading smart assets.
In a slight abuse of notation, we also call the main instance of the AVM 
"the AVM" since this is the one people almost always use.

## Bech32

Addresses on the X-Chain and P-Chain follow the Bech32 standard outlined in [BIP 0173](https://en.bitcoin.it/wiki/BIP_0173). There are four parts to this addressing scheme, in order of appearance:

1. A Human-Readable Part (HRP).
2. The number "1" as a separator (the last digit 1 seen is considered the separator).
3. Base-32 encoded string for the data-part of the address (the 20 byte address itself).
4. A 6-character base-32 encoded error correction code using the BCH algorithm.

This produces an address such as this:

```
/* X-Chain address */
X-avax1kj06lhgx84h39snsljcey3tpc046ze68mek3g5

/* P-Chain address */
P-avax1kj06lhgx84h39snsljcey3tpc046ze68mek3g5
```

In this case, the Bech32 breakdown is as follows:

1. An HRP of "avax".
2. The separator "1".
3. The base32 address part of "kj06lhgx84h39snsljcey3tpc046ze68".
4. A correction code of "mek3g5".

**Note:** the chainid is not considered part of the HRP of the Bech32 address. Both of these addresses represent the same private key, but on different chains.

The error correction code can be used to detect up to 4 errors with certainty in the entire address. While it can automatically correct these errors, because it's is uncertain how many errors may exist in the address when typed, it is inadvisable to correct them simply make suggestions where the errors MAY occur to the users. Automatic correct can result in assets being sent to the wrong place.

## Blockchain

A blockchain is an append-only ledger. Blockchains have the special property that nobody can re-write old sections of the ledger. 
Applications, such as payments systems, can be built atop blockchains. 

## CB58

CB58 is a format used to represent keys, addresses, and other binary values in web wallets and APIs. CB58 is the concatenation of the data bytes and a checksum. The checksum is created by taking the last four bytes of the SHA256 hash of the data bytes. 

This concatenated output is then mapped to a base-58 string. It uses 58 lowercase letters, uppercase letters, and digits so values are easier to read and write. The alphabet for the base-58 conversion is:

`123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz`

A full implementation of the base-58 format [can be found in AvalancheJS](https://github.com/ava-labs/avalanchejs/blob/master/src/utils/base58.ts)

CB58 is similar to [Base58Check], but uses a different checksum algorithm.

[Base58Check]: https://en.bitcoin.it/wiki/Base58Check_encoding

## Consensus Protocol

A consensus protocol is an algorithm, or series of steps, that a set of computers follow in order to reach agreement on some piece of information.
In a payments system, for example, the computers follow a protocol to agree on who has how much money.

## AvalancheGo

AvalancheGo is the Go implementation of an Avalanche node.

## Node

A node is a computer participating in the Avalanche network.

## Smart Digital Assets

A smart digital asset is a digital representation of a real-world thing such as an equity, bond, money etc.
They can have a complex ruleset that defines their behavior and how they are handled.
This ruleset can include things like, "can't be traded until next year," for example.

## [Subnetwork](../core-concepts/overview.md#what-are-subnets)

A subnetwork (or subnet) is a set of nodes that validate a set of blockchains.

## Transaction

A transaction queries or modifies the state of a blockchain.

## Validator

A validator is a node participating in the consensus protocol.
Validators work together to achieve consensus as to which transactions have taken place on a blockchain.

## [Virtual Machines](../core-concepts/overview.md#what-are-virtual-machines)

A Virtual Machine defines the application-level logic of a blockchain.
In technical terms, it defines a state machine.
It specifies the state that is held, the state transition function, and the transactions that clients can issue in order to query/modify the state.
Developers create Virtual Machines that implements some functionality and then create a blockchain that runs the Virtual Machine.
Every blockchain runs (is an instance of) a Virtual Machine.  