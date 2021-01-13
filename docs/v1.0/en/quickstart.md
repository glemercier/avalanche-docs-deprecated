---
title: "Avalanche Quickstart Guide"
excerpt: ""
---

# Quickstart Guide

The quickest way to learn about Avalanche is to run a node and interact with the network.

In this tutorial, which should take less than 10 minutes, we will:

* Install and run an Avalanche node
* Connect to the Avalanche Network
* Send AVAX, the Avalanche network's native token
* Add your node to the validator set


If you run into any issues at all, **please check the [FAQ](faq.md)**
If your issue isn't addressed there, come ask for help on our [Discord Server!](https://chat.avalabs.org/)
We will work to get you through any problems.

## Requirements 

Avalanche is an incredibly lightweight protocol, so the minimum computer requirements are quite modest. 

* Hardware: CPU > 2 GHz, RAM > 4 GB, Storage > 10 GB free space
* OS: Ubuntu 18.04/20.04 or Mac OS X >= Catalina
* Software: [Go](https://golang.org/doc/install) >= 1.13

Run `go version`. **It should be 1.13 or above.**
Run `echo $GOPATH`. **It should not be empty.**

This tutorial assumes you have enough AVAX to add a validator held under a mnemonic key phrase.

## Run an Avalanche Node

Let's install AvalancheGo, the Go implementation of an Avalanche node, and connect to the Avalanche Public Testnet.

### Download AvalancheGo

The node is a binary program. You can either download the source code and then build the binary program, or you can download the pre-built binary.
You can do either of the below. You don't need to do both.

#### Source Code

Download the AvalancheGo repository:

```sh
go get -v -d github.com/ava-labs/avalanchego/...
```

(Note to advanced users: AvalancheGo uses Go modules, so you can actually clone the [AvalancheGo repository](https://github.com/ava-labs/avalanchego) to locations other than your GOPATH.)

Change to the `avalanchego` directory:

```sh
cd $GOPATH/src/github.com/ava-labs/avalanchego
```

Build AvalancheGo:

```sh
./scripts/build.sh
```

The binary, named `avalanchego`, is in `avalanchego/build`. 

#### Binary

Go to our [releases page](https://github.com/ava-labs/avalanchego/releases) and select the release you want (probably the latest one.)

Under `Assets`, select the appropriate file.

For MacOS:  
Download the file named `avalanche-osx-<VERSION>.zip`  
Unzip the file with `unzip avalanche-osx-<VERSION>.zip`  
The resulting folder, `avalanche-<VERSION>`, contains the binaries.  
You can run the node with `./avalanche-<VERSION>/avalanchego`

For Linux:  
Download the file named `avalanche-linux-<VERSION>.tar.gz`.  
Unzip the file with `tar -xvf avalanche-linux-<VERSION>.tar.gz`  
The resulting folder, `avalanche-<VERSION>`, contains the binaries.  
You can run the node with `./avalanche-<VERSION>/avalanchego`

### Start a Node and Connect to the Avalanche Network

To start a node and connect it to the Avalanche Network:

If you built from source:
```sh
./build/avalanchego
```

If you are using the released binaries:
```sh
./avalanche-<VERSION>/avalanchego
```

If you want to be able to make API calls to your node from other machines, include argument `--http-host=` (e.g. `./build/avalanchego --http-host=`)

You can use `Ctrl + C` to kill the node.

To connect to the Fuji Testnet instead, use argument `--network-id=fuji`.
You can get funds on the Testnet from the [faucet.](https://faucet.avax-test.network/)

When the node starts, it has to bootstrap (catch up with the rest of the network.)
You will see logs about bootstrapping.
When a given chain is done bootstrapping, it will print a log like this:

`INFO [06-07|19:54:06] <X Chain> /snow/engine/avalanche/transitive.go#80: bootstrapping finished with 1 vertices in the accepted frontier`

To check if a given chain is done bootstrapping, call [info.isBootstrapped](api/info.md#infoisbootstrapped) like so:

```json
curl -X POST --data '{
    "jsonrpc":"2.0",
    "id"     :1,
    "method" :"info.isBootstrapped",
    "params": {
        "chain":"X"
    }
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/info
```

If this returns `true`, the chain is bootstrapped.
If you make an API call to a chain that is not done bootstrapping, it will return `API call rejected because chain is not done bootstrapping`.
If your node never finishes bootstrapping, contact us on [Discord.](https://chat.avalabs.org/)

## Create a Keystore User

Avalanche nodes provide a built-in **Keystore.**
The Keystore manages users and is a lot like a wallet.
A user is a password-protected identity that a client can use when interacting with blockchains.
**You should only create a keystore user on a node that you operate, as the node operator has access to your plaintext password.**
To create a user, call `keystore.createUser`:

```sh
curl -X POST --data '{
     "jsonrpc": "2.0",
     "id": 1,
     "method": "keystore.createUser",
     "params": {
         "username": "YOUR USERNAME HERE",
         "password": "YOUR PASSWORD HERE"
     }
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/keystore
```

The response should be:

```json
{
     "jsonrpc":"2.0",
     "result":{"success":true},
     "id":1
}
```

Now you have a user on this node.
Keystore data exists at the node level.
Users you create on one node's Keystore do not exist on
other nodes but you can import/export users to/from the Keystore.
See the [Keystore API](api/keystore.md) to see how.

**You should only keep a small amount of your funds on your node.**
Most of your funds should be secured by a mnemonic that is not saved to any computer.

## Create an Address

Avalanche is a network of heterogeneous blockchains.
One of these blockchains is the X-Chain, which acts as a decentralized platform for creating and trading digital assets.
(Think X for eXchanging assets.)

There's a special asset on the X-Chain called the [AVAX token](core-concepts/overview.md#the-x-chain). 
This is the native token of the Avalanche network, and transaction fees on the Avalanche network are paid in AVAX.

Let's create an address to hold AVAX tokens on our node.

To create a new address on the X-Chain, call `avm.createAddress`, a method of the [X-Chain's API](api/avm.md):

```sh
curl -X POST --data '{
    "jsonrpc":"2.0",
    "id"     :2,
    "method" :"avm.createAddress",
    "params" :{
        "username":"YOUR USERNAME HERE",
        "password":"YOUR PASSWORD HERE"
    }
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/bc/X
```

If your node isn’t finished bootstrapping, this call will return status `503` with message `API call rejected because chain is not done bootstrapping`.

Note that we make this request to `127.0.0.1:9650/ext/bc/X`.
The `bc/X` portion signifies that the request is being sent to the blockchain whose ID (or alias) is `X` (ie the X-Chain.)

The response should look like this:

```json
{
    "jsonrpc":"2.0",
    "id":2,
    "result" :{
        "address":"X-avax1xeaj0h9uy7c5jn6fxjp0rg4g39jeh0hl27vf75"
    }
}
```

Your user now controls the address `X-avax1xeaj0h9uy7c5jn6fxjp0rg4g39jeh0hl27vf75` on the X-Chain.
To tell apart addresses on different chains, the Avalanche convention is for an address to include the ID or alias of the chain it exists on.
Hence, this address begins `X-`, denoting that it exists on the X-Chain.

## Send Funds From the Web Wallet to Your Node

**Note: the instructions below move real funds.**

Let's move funds from your web wallet to your node.

Go to [the wallet](https://wallet.avax.network). Click `Access Wallet`, then `Mnemonic Key Phrase`.
Enter your mnemonic phrase. 
(Note: In the near future Avalanche will support hardware wallets so that you don't need to enter your mnemonic to do this.)


Click the `Send` tab on the left.
For amount, select, `.002` AVAX.
Enter the address of your node, then click `Confirm`.

![Send fund to node](../../images/tutorials/quickstart/1.png)

We can check an address's balance of a given asset by calling `avm.getBalance`, another method of the X-Chain's API.
Let's check that the transfer went through:

```sh
curl -X POST --data '{
    "jsonrpc":"2.0",
    "id"     :3,
    "method" :"avm.getBalance",
    "params" :{
        "address":"X-avax1xeaj0h9uy7c5jn6fxjp0rg4g39jeh0hl27vf75",
        "assetID"  :"AVAX"
    }
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/bc/X
```

Note that AVAX has the special ID `AVAX`. 
Usually an asset ID is an alphanumeric string.

The response should indicate that we have `2,000,000` nAVAX, or .002 AVAX.

```json
{
    "jsonrpc":"2.0",
    "id"     :3,
    "result" :{
        "balance":2000000,
        "utxoIDs": [
            {
                "txID": "x6vR85YPNRf5phpLAEC7Sd6Tq2PXWRt3AAHAK4BpjxyjRyhtu",
                "outputIndex": 0
            }
        ]
    }
}
```

## Send AVAX

Now let's send some AVAX by making an API call to our node:

```sh
curl -X POST --data '{
    "jsonrpc":"2.0",
    "id"     :5,
    "method" :"avm.send",
    "params" :{
        "assetID"    :"AVAX",
        "amount"     :1000,
        "to"         :"X-avax1w4nt49gyv4e99ldqevy50l2kz55y9efghep0cs",
        "changeAddr" :"X-avax1turszjwn05lflpewurw96rfrd3h6x8flgs5uf8",
        "username"   :"YOUR USERNAME HERE",
        "password"   :"YOUR PASSWORD HERE"
    }
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/bc/X
```

`amount` specifies the number of nAVAX to send.

If you want to specify a particular address where change should go, you can specify it in `changeAddr`.
You can leave this field empty; if you do, any change will go to one of the addresses your user controls.

In order to prevent spam, Avalanche requires the payment of a [transaction fee](transaction-fees.md).
The transaction fee will be automatically deducted from an address controlled by your user when you issue a transaction.
Keep that in mind when you're checking balances below.
The transaction fee schedule is [here.](transaction-fees.md)

When you send this request, the node will authenticate you using your username and password.
Then, it will look through all the private keys controlled by your user until it finds
enough AVAX to satisfy the request.

The response contains the transaction's ID. It will be different for every invocation of `send`.

```json
{
    "jsonrpc":"2.0",
    "id"     :5,
    "result" :{
        "txID":"2QouvFWUbjuySRxeX5xMbNCuAaKWfbk5FeEa2JmoF85RKLk2dD",
        "changeAddr" :"X-avax1turszjwn05lflpewurw96rfrd3h6x8flgs5uf8"
    }
}
```

## Checking the Transaction Status

This transaction will only take a second or two to finalize. We can check its status with `avm.getTxStatus`:

```sh
curl -X POST --data '{
    "jsonrpc":"2.0",
    "id"     :6,
    "method" :"avm.getTxStatus",
    "params" :{
        "txID":"2QouvFWUbjuySRxeX5xMbNCuAaKWfbk5FeEa2JmoF85RKLk2dD"
    }
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/bc/X
```

The response should indicate that the transaction was accepted:

```json
{
    "jsonrpc":"2.0",
    "id"     :6,
    "result" :{
        "status":"Accepted"
    }
}
```

You might also see that `status` is `Processing` if the network has not yet finalized the transaction.

Once you see that the transaction is `Accepted`, check the balance of the `to` address to see that it has the AVAX we sent:

```sh
curl -X POST --data '{
    "jsonrpc":"2.0",
    "id"     :7,
    "method" :"avm.getBalance",
    "params" :{
        "address":"X-avax1w4nt49gyv4e99ldqevy50l2kz55y9efghep0cs",
        "assetID"  :"AVAX"
    }
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/bc/X
```

The response should be:

```json
{
    "jsonrpc":"2.0",
    "id"     :7,
    "result" :{
        "balance":1000
    }
}
```

In the same fashion, we could check `X-avax1xeaj0h9uy7c5jn6fxjp0rg4g39jeh0hl27vf75` to see that AVAX we sent was deducted from its balance, as well as the transaction fee.

## Validate the Primary Network (Stake)

See [here](tutorials/adding-validators.md) for a tutorial on adding a node to the validator set.

## Next Steps

- [Learn more about Avalanche](core-concepts/overview.md)
- [Create and trade a new asset](tutorials/fixed-cap-asset.md)
- [Create a new blockchain](tutorials/create-a-blockchain.md)
- [Create a new, custom validator set (Subnet)](tutorials/create-a-subnet.md)
- [Explore our APIs](api/intro-apis.md)
- [Explore reference materials](references/cryptographic-primitives.md)
- [Read the whitepaper and other papers](papers/index.md)
