---
title: "FAQ"
excerpt: ""
---


This guide addresses frequently asked questions.

We are working very hard to answer everyone's questions, but there are only so many of us, so 
**please try to find the answer to your question before asking on Discord.**
Look on this page first, and if you don't find the answer here, search on Discord. 
If you're having an issue, chances are high that somebody else had the same issue.
If your question is still unanswered, ask about it on our [Discord](https://chat.avalabs.org) server.

**There is a list of known issues at the bottom of this page.**

## Where can I go for help?

[Discord](https://chat.avalabs.org), but please search the documentation and Discord for your issue before asking about it.

## I think something went wrong or there's a bug. What do I do?

First, re-read any instructions that you were following and make sure that you followed them correctly.
See [known issues](#known-issues) and the #mainnet-status channel of our [Discord](https://chat.avalabs.org).
If your issue is not addressed anywhere, post in the appropriate Discord channel (probably #troubleshooting.)

If, after doing the above, you think there is a bug, please make an issue on our [Github.](https://github.com/ava-labs/avalanchego/issues) 
Please give as much information and context as possible when describing an issue.
If someone already reported the same issue, comment with your details.

## Is there a test net faucet?

[Yes.]({{https://faucet.avax.network/}})

## Is there a browser-based wallet?

[Yes.](https://wallet.avax.network/)

## Is there an explorer?

[Yes.](https://explorer.avax.network/)

## Is there an indexer?

[Yes.](https://github.com/ava-labs/ortelius)

## Is there a Javascript library?

[Yes.](../../tools/avalanchejs/)

## How do transaction fees work?

[See here.](transaction-fees.md)

## Does Avalanche support hardware wallets?

[Yes.](https://medium.com/avalabs/how-to-set-up-your-ledger-nano-s-with-avalanche-4e5d385410d4)

## Running a node

### How do I upgrade my node?

The node is a binary program. You can either download the source code and then build the binary program, or you can download the pre-built binary.
You can do either of the below. You don't need to do both.

#### From source

First clone our Github repo (you can skip this step if you've done this before):

`git clone https://github.com/ava-labs/avalanchego.git`

Then move to the `avalanchego` directory:

`cd avalanchego`

Pull the latest code:

`git pull`

Check that your local code is up to date. Do:

`git rev-parse HEAD`

and check that the first 7 characters printed match the `Latest commit` field on our [Github.](https://github.com/ava-labs/avalanchego)

Now build the binary:

`./scripts/build.sh`

This should print `Build Successful`.

You can check what version you're running by doing `./build/avalanchego --version`.

You can run your node with `./build/avalanchego`

#### Download Binary

Go to our [releases page](https://github.com/ava-labs/avalanchego/releases) and select the release you want (probably the latest one.)

Under `Assets`, select the appropriate file.

For MacOS:  
Download the file named `avalanche-osx-<VERSION>.zip`  
Unzip the file with `unzip avalanche-osx-<VERSION>.zip`  
The resulting folder, `avalanche-<VERSION>`, contains the binaries.  
You can run the node with `./avalanche-<VERSION>/avalanche`

For Linux x86:  
Download the file named `avalanche-linux-<VERSION>.tar.gz`  
Unzip the file with `tar -xvf avalanche-linux-<VERSION>.tar.gz`  
The resulting folder, `avalanche-<VERSION>`, contains the binaries.  
You can run the node with `./avalanche-<VERSION>/avalanche`

Note: The Linux binaries are compiled for AMD64 (x86-64) architectures. For ARM platforms, like the Raspberry Pi, please [build from source](#from-source).

### What hardware do I need?

In general, any fairly modern computer should do.
It should have:

* CPU >= 2 GHz
* RAM >= 4 GB
* Free disk space >= 10 GB

### What software do I need?

Your operating system should be 64-bit Ubuntu 18.04/20.04 or Mac OS >= Catalina.
Other operating systems may work but are not well tested.

You need to have Go 1.14+ installed.
To check, do: `go version`.
If Go is not installed, follow one of the many online guides for doing so.

You also need to have your GOPATH set.
To check that it's set, do `echo $GOPATH`. It should print something.
If it doesn't see [here](https://www.digitalocean.com/community/tutorials/how-to-install-go-on-ubuntu-18-04) or one of the many of the guides about setting your GOPATH.

### Is my node connected to peers?

Call `info.peers`:

```sh
curl -X POST --data '{
    "jsonrpc":"2.0",
    "id"     :1,
    "method" :"info.peers"
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/info
```

Each entry in the response contains a peer's IP address, public IP address, node ID, version, and the time of the last sent and received messages exchanged with this node.
If you see none, something is wrong and you are not connected to any peers.

### Do I need to open any ports?

[See here.](staking.md#networking)

### How can I set up node monitoring?

See [this tutorial.](tutorials/node-monitoring.md)

### Why does my node not show as connected to another node? Why is my uptime being reported as being low?

See [here.](staking.md#networking)

### What should I back up?

You should back up your Staking Key and Certificate, which determines your node's ID.
By default these are at `$HOME/.avalanchego/staking/staker.key` and ``$HOME/.avalanchego/staking/staker.key`.
To transfer your node to a new machine, place these files on the new machine at `$HOME/.avalanchego/staking`

You should also periodically back up your keystore user by calling `keystore.exportUser`:

```json
curl --location --request POST 'http://localhost:9650/ext/keystore' \
--header 'Content-Type: application/json' \
--data-raw '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "keystore.exportUser",
    "params": {
        "username": "USERNAME",
        "password": "PASSWORD"
    }
}'
```

You can import your user to a new machine by calling `keystore.importUser`:

```json
{
    "jsonrpc":"2.0",
    "id"     :1,
    "method" :"keystore.importUser",
    "params" :{
        "username": "USERNAME",
        "password": "PASSWORD",
        "user"    :"1191JUhdGwFrYiakadtxRxvEo4gwESQkHD2wRZueSpS2FhF3d29FaBTosvRd9iMd1Uk5MbnwEZkp47ukbyqyphKyq9uut7Z4cqrRr6p7KJFscVYgdkhZNjVj72KSCLE7W6zhkFXYu7JbX1tXYFZ5WTKDHLNXLgh7H7vB43sYwWDXUAeVaf8mKsdvhhEBTo3NYxdwUcETs9bKkepmZ84muzisS7LHKC9W3vyX5YE9xMjehzHrGi6MDwpxgu8AUvTeWoM9Lpp2t8stykej8BWAFiVrqWoyjLsPRCnay7W8ksYfWLaHps8acQkuSwCFWsnWT38tiAz3NxvowqF9i21CnJM9hBvbu3TWbE5k2TYmBa28ZTy1paJwRaz78uHgAX5TugujiQqM4kcEDtWr5Xa5Q7z5c4XoRbprWwsbTdXcDGszdV1hjDiApCBMtLexmDLXjFuFv3NY9Y5ULSES4c7kqXvfhwATrVBdJTZSdayLrqCQ1fKEvQ6GvLtcGPWM6bUR7iFtT74zR217376aBi7M8d6s65PVrzXqBVyFWhc1sPwdxJanJewhBqoVycfyNDeJ32WLHwCRKCFDAD4WLB5vNXxsgv1GbjmZ9wRpte2SysHkyDFhK9HwUfzjkCkZGugDbXg3fYkLDSDGfYe2caWGJ8R7JsKytaKnby8yjqsGqvrXwyB3uT1CWfvH4grNaSgtU9aiviVecmvpjEznuNDsp2ztVxvrPsFetZHq8mCfrhiumfPqToo9C8xRcV2diUmCVCTyFczTqgkGe7d5tp3miATDS2ALof73K2fJcDKwNVenfDudQQULxSrMxFFXnUfq2SpcE6MDiS9PY4PYqv9umbWJq4s4DbBBV7ZY7ed"
    }
}
```


Make sure that all backups are secure and accessible only by you.

### Will restarting/upgrading my node give me a new node ID?

No.

### What version am I running?

Run `./avalanchego --version`.
It will print the version of Avalanche you're running.
To see what the latest release is, see our [releases page.](https://github.com/ava-labs/avalanchego/releases/)

### Why am I getting a 404 when I make an API call?

First, make sure you're sending the API call to the right place/following an instruction correctly.
Then, [make sure your node is connected to peers.](#is-my-node-connected-to-peers)
If a problem persists, contact us on [Discord.](https://chat.avalabs.org)

### Where is my node's data?

By default, the node's database is at `$HOME/.avalanchego/db`
**If you delete this directory, it will erase the state of your node, including private keys and keystore users.**

If there is a new major release (e.g. Everest --> Fuji), your node's state will be reset.
Otherwise, upgrading AvalancheGo will not overwrite or destroy your node's state.

The only data not in this folder is your node's Staking Key/Certificate.
These are in `$HOME/.avalanchego/staking` by default.
**If you delete this directory, it will erase your node ID. This will jeopardize your validator reward.**
If your node is a validator and you delete its staking key, it will no longer be a validator when it restarts.

Upgrading AvalancheGo will never erase or change your Staking Key/Certificate.

### Where are my node's logs?

By default, they're in `$HOME/.avalanchego/logs/node`.
Logs specific to a chain are in subdirectory `chain/[CHAIN ID]` where `[CHAIN ID]` is the chain's ID.

### Can I run AvalancheGo on a different machine but keep the node ID / state?

Yes, see [here.](#what-should-i-back-up)

### Is my node done bootstrapping?

Each chain bootstraps separately and finishes bootstrapping at different times.

To check whether a given chain is done bootstrapping, call API method [`info.isBootstrapped`.](api/info.md#infoisbootstrapped)
For example, to see if the X-Chain is done bootstrapping:

```sh
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "info.isBootstrapped",
    "params":{
        "chain":"X"
    },
    "id": 1
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/info
```

### How do I kill my node?

Do `CTRL + C` in the terminal window where you're running the node.

There is a known issue where sometimes this doesn't kill the node.
In that case:

Do `CTRL + Z` in the terminal window where you're running the node.
This should print something like:

```sh
[1]+  Stopped     /home/youruser/go/src/github.com/ava-labs/avalanchego/build/avalanche
```

Do `kill -9 %1` (or `kill -9 %2` if it printed `[2]+ Stopped`, etc.)

## Staking

### How does staking work?

[See here.](staking.md)

### Does Avalanche use slashing?

There is no stake slashing on the Avalanche network. 
If a validator misbehaves, they will not receive a reward, but it will not lose the funds it staked.

### What is the minimum amount I need to stake in order to validate the Primary Network?

2,000 AVAX.

### What is the minimum amount I can delegate on the Primary Network?

25 AVAX.

### Is my node in the validator set?

First, get your node's ID:

```sh
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "info.getNodeID",
    "params":{},
    "id": 1
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/info
```

The response contains your node's ID:

```json
{
    "jsonrpc": "2.0",
    "result": {
        "nodeID": "NodeID-ARCLrphAHZ28xZEBfUL7SVAmzkTZNe1LK"
    },
    "id": 1
}
```

Then look on the [explorer's validator page](https://explorer.avax.network/validators) and verify your node is present.

You can check with this API call, which gets the list of validators:

```json
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "platform.getCurrentValidators",
    "params": {},
    "id": 4
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/P
```

Check if your node is in that list.

If it's not there, check the pending validator list:

```json
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "platform.getPendingValidators",
    "params": {},
    "id": 4
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/P
```

If your node is not in either list, it is not a validator.

Please note that even if your node is off, it will appear in the validator list.
In order to receive staking rewards, your node must also be running and connected to other nodes.

## How can I get involved with Avalanche?

We want you to be a part of the Avalanche community!
Any contribution, no matter how small, is valued and welcome.

You can:

* [Get a grant](https://www.avalabs.org/avalanche-x) for working on a project that adds to the Avalanche ecosystem
* [Participate in our bug bounty](https://www.avalabs.org/avalanche-x/explore-open-grants/bug-bounty)
* Check out the #dev-chat channel on our [Discord.](https://chat.avalabs.org)
* Make an issue or pull request on our [Github.](https://github.com/ava-labs/avalanchego)

## How can I get all transactions?

You can use [Ortelius](https://github.com/ava-labs/ortelius), our indexer.

## What is the `id` field in every API call? Do I need to change it?

AvalancheGo uses the [JSON RPC 2.0 standard](https://www.jsonrpc.org/specificatio) for API calls.  
As part of this standard, each call and response has a field `id`.
This is useful in tracking which response corresponds to which request.

You do not need to change this field when making API calls.
It's OK to make many API calls with the same `id` field.

### What are AVAX's units?

1 AVAX == 1,000 milliAVAX  
1 milliAVAX == 1,000 microAVAX  
1 microAVAX === 1,000 nAVAX

## Web Wallet

### What is the HD Path?

`m / 44' / 9000'`

### My address keeps changing after each transaction. Why?

HD wallets support generating a new address for every transaction. This is a convention to increase privacy. Previous addresses may be reused and your wallet's balance will be correct.

## Common Problems

### Networking Setup

See [here](../en/references/command-line-interface.md) for all command line arguments.

It's important that your node is well connected to other nodes.
It needs to be able to create outgoing connections to other nodes as well as accept incoming connections.
This means that:

* Your node must know its public IP
* Your node must be able to receive traffic on the P2P port (`9651` by default)

#### Public IP

If your node is on a machine with a static IP (e.g. AWS Elastic IP) then you should run your node with `--public-ip=[x.x.x.x]`.

If not, you should configure your node to fetch its public IP from a web service.
To do so, run with `--dynamic-public-ip=opendns` or `--dynamic-public-ip=ifconfig`.

If you use neither of the above arguments, your node will attempt to use UPnP or NAT-PMP to fetch your public IP from your router.
Your router needs to have one of these features enabled, and you should allow UDP traffic from port 1900 to the node machine for UPnP.

#### Open P2P Port

If you run a node with `--public-ip` or `--dynamic-public-ip` then you must manually allow incoming TCP traffic the P2P port (`9651` by default.)
If the node machine is behind a router, you should set up a port forwarding rule.
If your node is on a cloud service, you should update your network security settings.
If you use neither of the above arguments, your node will try to set up a port forwarding rule on your router, if it exists and supports UPnP or NAT-PMP.

#### What is my public IP

You can use either of the following commands:

```
$ curl ifconfig.co
```

```
$ dig +short myip.opendns.com @resolver1.opendns.com
$ dig +short -4 myip.opendns.com @resolver1.opendns.com
```

#### Node prints `UPnP or NAT-PMP router attach failed, you may not be listening publicly ...` on startup

The message indicates a dynamic port forwarding may not have worked.
Check your router settings to ensure that you have these setting enabled, and restart the node.

#### Node prints `failed with goupnp: SOAP request got HTTP 500 Internal Server Error`

Your node is attempting UPnP, and is having issues mapping on your router.
Your router might have manually created port forwarding rules.
Ensure that you do not have conflicting port forwarding rules on your router.

### Node only responds to API calls made from localhost

Use command line argument `--http-host=` or `--http-host=0.0.0.0` when you run your node.

### API call fails with `Failed to connect to 127.0.0.1 port 9650: Connection refused`

This means that your node is not listening for HTTP traffic on localhost port 9650.
There are a few reasons this might happen.

First, make sure that your node is actually running. If you're running the node in a terminal, **you have to be running the node in a separate tab/window from the one you're using to make the API calls.** If you do `CTRL + C` to kill your node and then make an API call, **it will fail because the node is not running.**

If you're running a node on another machine, replace `localhost` or `127.0.0.1` in the API call with the machine's IP address.

If you started your node with command-line argument `--http-port=9700` then replace `9650` in the API call with `9700` (or whatever port you specified.)

#### Starting node fails with: `parsing parameters returned with error couldn't create db at ...`

There is already a node running on your machine.

### Node is on the wrong network

If you have already received funds from the faucet and can see them on the explorer, but you still can't send a transaction with your node, you may still be connected to an old test network.
Check to make sure that you are are on the right network, by calling `info.getNetworkName`:

```json
curl -X POST --data '{
    "jsonrpc":"2.0",
    "id"     :1,
    "method" :"info.getNetworkName"
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/info
```

The expected output is:

```json
{
    "jsonrpc": "2.0",
    "result": {
        "networkID": "mainnet"
    },
    "id": 1
}
```

If the response is not `mainnet`, you are not on the Mainnet.

### API response says `invalid character 'â' looking for beginning of value`

You probably pasted the API call from a program that uses smart quotes.
Replace each character that looks like a quotation mark " with a quotation mark and try again.

### Starting node fails with: `cannot execute binary file: Exec format error`

You are probably using one of the [official releases](https://github.com/ava-labs/avalanchego/releases) on an architecture it was not build for.  
The `avalanche-linux-<release>.tar.gz` is build for AMD64 (x86-64) architectures. Platforms based on ARM chips, like the Raspberry Pi, are **not** working with it.  
Instead you need to [build AvalancheGo from source](quickstart.md#download-avalanchego-source-code). Keep in mind you need to use a 64-bit operating system.

## Known Issues/Bugs

This section contains bugs and issues that we're aware of.
Please also see our [Github issues.](https://github.com/ava-labs/avalanchego/issues)

### Node won't quit with `CTRL + C`

If your node is running in an open terminal tab:

Do `CTRL + Z` in the terminal window where you're running the node.
This should print something like:

```sh
[1]+  Stopped     /home/youruser/go/src/github.com/ava-labs/avalanchego/build/avalanche
```

Do `kill -9 %1` (or `kill -9 %2` if it printed `[2]+ Stopped`, etc.)

If your node is running in the background:

Do `ps aux | grep avalanchego`

The output should have a line that looks like this:

```sh
youruser 29861  8.7  0.2 1459208 34996 pts/2   Sl+  19:44   0:00 ./build/avalanchego
```

Do:

`kill -9 29861` (or whatever number appears in the second column)
