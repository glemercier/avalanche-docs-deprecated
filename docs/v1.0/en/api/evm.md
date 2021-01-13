# EVM API

This document describes the API of the C-Chain, which is an instance of the Ethereum Virtual Machine (EVM.)

Note: Ethereum has its own notion of `networkID` and `chainID`. 
These have no relationship to Avalanche's view of networkID and chainID, and are purely internal to the C-Chain. 
On Mainnet, the C-Chain uses `1` and `43114` for these values.
On the Fuji Testnet, it uses `1` and `43113` for these values.
`networkID` anc `chainID` can also be obtained using the `net_version` and `eth_chainId` methods shown below.
## Deploying a Smart Contract

For a tutorial on deploying a Solidity smart contract on the C-Chain, see [here.](../tutorials/deploy-a-smart-contract.md)

## Methods

This API is identical to Geth's API except that it only supports the following services:

* `web3_`
* `net_`
* `eth_`
* `personal_`
* `txpool_`

You can interact with these services the same exact way you'd interact with Geth.
See the [Ethereum Wiki's JSON-RPC Documentation](https://eth.wiki/json-rpc/API) and [Geth's JSON-RPC Documentation](https://geth.ethereum.org/docs/rpc/server) for a full description of this API.


## JSON-RPC Endpoints

To interact with C-Chain (the main EVM instance on Avalanche):

```http
/ext/bc/C/rpc
```

To interact with other instances of the EVM:

```http
/ext/bc/blockchainID/rpc
```

where `blockchainID` is the ID of the blockchain running the EVM.

To interact with the `avax` specific RPC calls

```http
/ext/bc/C/ava
```

## WebSocket-RPC Endpoints

To interact with C-Chain (the main EVM instance on Avalanche):

```http
/ext/bc/C/ws
```

To interact with other instances of the EVM:

```http
/ext/bc/blockchainID/ws
```

where `blockchainID` is the ID of the blockchain running the EVM.

## Examples

### Getting the current client version

#### Example Call

```json
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "web3_clientVersion",
    "params": [],
    "id": 1
}' -H 'Content-Type: application/json' \
   -H 'cache-control: no-cache' \
   127.0.0.1:9650/ext/bc/C/rpc 
```

#### Example Response

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "Athereum 1.0"
}
```

### Getting the network ID

#### Example Call

```json
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "net_version",
    "params": [],
    "id": 1
}' -H 'Content-Type: application/json' \
   -H 'cache-control: no-cache' \
   127.0.0.1:9650/ext/bc/C/rpc 
```

#### Example Response

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "1"
}
```

### Getting the chain ID

Not well documented in JSON-RPC references. See instead [EIP694](https://github.com/ethereum/EIPs/issues/694)

#### Example Call

```json
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "eth_chainId",
    "params": [],
    "id": 1
}' -H 'Content-Type: application/json' \
   -H 'cache-control: no-cache' \
   127.0.0.1:9650/ext/bc/C/rpc 
```

#### Example Response

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "0xa866"
}
```

### Getting the most recent block number

#### Example Call

```json
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "eth_blockNumber",
    "params": [],
    "id": 1
}' -H 'Content-Type: application/json' \
   -H 'cache-control: no-cache' \
   127.0.0.1:9650/ext/bc/C/rpc 
```

#### Example Response

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "0x10f"
}
```

### Getting an account's balance

#### Example Call

```json
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "eth_getBalance",
    "params": [
        "0x820891f8b95daf5ea7d7ce7667e6bba2dd5c5594",
        "latest"
    ],
    "id": 1
}' -H 'Content-Type: application/json' \
   -H 'cache-control: no-cache' \
   127.0.0.1:9650/ext/bc/C/rpc 
```

#### Example Response

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "0x0"
}
```

### Getting an account's nonce

#### Example Call

```json
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "eth_getTransactionCount",
    "params": [
        "0x820891f8b95daf5ea7d7ce7667e6bba2dd5c5594",
        "latest"
    ],
    "id": 1
}' -H 'Content-Type: application/json' \
   -H 'cache-control: no-cache' \
   127.0.0.1:9650/ext/bc/C/rpc 
```

#### Example Response

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "0x0"
}
```

### Calculate a cryptographic hash

The input parameter contains hexidecimal bytes of arbitrary length. The example here uses the UTF-8 text string "snowstorm" converted to hexidecimal bytes.

#### Example Call

```json
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "web3_sha3",
    "params": [
        "0x736e6f7773746f726d"
    ],
    "id": 1
}' -H 'Content-Type: application/json' \
   -H 'cache-control: no-cache' \
   127.0.0.1:9650/ext/bc/C/rpc 
```

#### Example Response

```json
{"jsonrpc":"2.0","id":1,}
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "0x627119bb8286874a15d562d32829613311a678da26ca7a6a785ec4ad85937d06"
}
```

### Creating a new account (private key generated automatically)

The EVM will create a new account using the passphrase `cheese` to encrypt and store the new account credentials. `cheese` is not the seed phrase and cannot be used to restore this account from scratch. Calling this function repeatedly with the same passphrase will create multiple unique accounts. Also keep in mind there are no options to export private keys stored in the EVM database. Users are encouraged to use wallet software instead for safer account creation and backup. This method is more suitable for quick account creation for a testnet.

#### Example Call

```json
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "personal_newAccount",
    "params": [
        "cheese"
    ],
    "id": 1
}' -H 'Content-Type: application/json' \
   -H 'cache-control: no-cache' \
   127.0.0.1:9650/ext/bc/C/rpc 
```

#### Example Response

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "0xa64b27635c967dfe9674926bc004626163ddce97"
}
```

### Creating a new account (using plaintext private key)

If the private key is known upfront, it can be provided as plaintext to load into the EVM account database. For more secure account management, consider using wallet software instead. The example below loads the private key `0x627119bb8286874a15d562d32829613311a678da26ca7a6a785ec4ad85937d06` with the passphrase `this is my passphrase`. Note that `0x` prefix cannot be included in the private key argument, otherwise the EVM will throw an error. The example response returns the associated public key.

#### Example Call

```json
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "personal_importRawKey",
    "params": [
        "627119bb8286874a15d562d32829613311a678da26ca7a6a785ec4ad85937d06",
        "this is my passphrase"
    ],
    "id": 1
}' -H 'Content-Type: application/json' \
   -H 'cache-control: no-cache' \
   127.0.0.1:9650/ext/bc/C/rpc 
```

#### Example Response

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "0x1c5b0e12e90e9c52235babad76cfccab2519bb95"
}
```

### Listing accounts loaded in EVM node

#### Example Call

```json
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "personal_listAccounts",
    "params": [],
    "id": 1
}' -H 'Content-Type: application/json' \
   -H 'cache-control: no-cache' \
   127.0.0.1:9650/ext/bc/C/rpc 
```

#### Example Response

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": [
        "0xa64b27635c967dfe9674926bc004626163ddce97",
        "0x1c5b0e12e90e9c52235babad76cfccab2519bb95"
    ]
}
```

### Unlocking an account

Personal accounts loaded directly in the EVM can only sign transactions while in an unlocked state. The example below unlocks the listed account address for 60 seconds. Note the associated passphrase `cheese` must be provided for authorization.

#### Example Call

```json
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "personal_unlockAccount",
    "params": [
        "0xa64b27635c967dfe9674926bc004626163ddce97",
        "cheese",
        60
    ],
    "id": 1
}' -H 'Content-Type: application/json' \
   -H 'cache-control: no-cache' \
   127.0.0.1:9650/ext/bc/C/rpc 
```

#### Example Response

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": true
}
```

### Signing a transaction

This method will create a signed transaction, but will not publish it automatically to the network. Instead, the `raw` result output should be used with `eth_sendRawTransaction` to execute the transaction.

#### Example Call

```json
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "eth_signTransaction",
    "params": [{
        "from": "0xa64b27635c967dfe9674926bc004626163ddce97",
        "to": "0x1c5b0e12e90e9c52235babad76cfccab2519bb95",
        "gas": "0x5208",
        "gasPrice": "0x0",
        "nonce": "0x0",
        "value": "0x0"
    }],
    "id": 1
}' -H 'Content-Type: application/json' \
   -H 'cache-control: no-cache' \
   127.0.0.1:9650/ext/bc/C/rpc 
```

#### Example Response

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "raw": "0xf8628080825208941c5b0e12e90e9c52235babad76cfccab2519bb958080830150efa0308ca8002f3df1a468eea9973d2d618eb866e2ef0a57cba4d34efb3025b70a0aa0592b7b0a803e7b70ec26dd74ab85aa71126198eff5552e5be638e6e26a455ee0",
        "tx": {
            "nonce": "0x0",
            "gasPrice": "0x0",
            "gas": "0x5208",
            "to": "0x1c5b0e12e90e9c52235babad76cfccab2519bb95",
            "value": "0x0",
            "input": "0x",
            "v": "0x150ef",
            "r": "0x308ca8002f3df1a468eea9973d2d618eb866e2ef0a57cba4d34efb3025b70a0a",
            "s": "0x592b7b0a803e7b70ec26dd74ab85aa71126198eff5552e5be638e6e26a455ee0",
            "hash": "0xda2fe3e76501e7201b1603a5d1b2e45c79240d623eeab0365aeba843a678f048"
        }
    }
}
```

### Send a raw transaction

Example below shows a raw transaction published to the network and its associated transaction hash.

#### Example Call

```json
curl -X POST --data '{
    "id": 1,
    "jsonrpc": "2.0",
    "method": "eth_sendRawTransaction",
    "params": [
        "0xf8628080825208941c5b0e12e90e9c52235babad76cfccab2519bb958080830150efa0308ca8002f3df1a468eea9973d2d618eb866e2ef0a57cba4d34efb3025b70a0aa0592b7b0a803e7b70ec26dd74ab85aa71126198eff5552e5be638e6e26a455ee0"
    ]
}' -H 'Content-Type: application/json' \
   -H 'cache-control: no-cache' \
   127.0.0.1:9650/ext/bc/C/rpc 
```

#### Example Response

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "0xda2fe3e76501e7201b1603a5d1b2e45c79240d623eeab0365aeba843a678f048"
}
```

### Call a contract

#### Example Call

```json
curl -X POST --data '{
    "id": 1,
    "jsonrpc": "2.0",
    "method": "eth_call",
    "params": [
        {
            "to": "0x197E90f9FAD81970bA7976f33CbD77088E5D7cf7",
            "data": "0xc92aecc4"
        },
        "latest"
    ]
}' -H 'Content-Type: application/json' \
   -H 'cache-control: no-cache' \
   127.0.0.1:9650/ext/bc/C/rpc 
```

#### Example Response

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "0x"
}
```

### Getting a block by hash

#### Example Call

```json
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "eth_getBlockByHash",
    "params": [
        "0x14d9c2aeec20254d966a947e23eb3172ae5067e66fd4e69aecc3c9d6ff24443a",
        true
    ],
    "id": 1
}' -H 'Content-Type: application/json' \
   -H 'cache-control: no-cache' \
   127.0.0.1:9650/ext/bc/C/rpc
```

#### Example Response

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "difficulty": "0x1",
        "extraData": "0x64616f2d686172642d666f726b47d8526faa68dca2174ea0a22994d5ca5c1f9ee77a6d6281ba81b0aaf3a972ae",
        "gasLimit": "0x5ee7167",
        "gasUsed": "0x5208",
        "hash": "0x14d9c2aeec20254d966a947e23eb3172ae5067e66fd4e69aecc3c9d6ff24443a",
        "logsBloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
        "miner": "0x0100000000000000000000000000000000000000",
        "mixHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
        "nonce": "0x0000000000000000",
        "number": "0x5",
        "parentHash": "0xc4eb127333754eac38fbd0ef4d036fb6ba39cda0fd3600e2ff91447148f4ef07",
        "receiptsRoot": "0x056b23fbba480696b65fe5a59b8f2148a1299103c4f57df839233af2cf4ca2d2",
        "sha3Uncles": "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
        "size": "0x29e",
        "stateRoot": "0xaf8c1c4dc0eaf6f95ff1a30d0353184e0aa415180bcc32abce9db919f7269961",
        "timestamp": "0x5ed4adf7",
        "totalDifficulty": "0x5",
        "transactions": [
            {
                "blockHash": "0x14d9c2aeec20254d966a947e23eb3172ae5067e66fd4e69aecc3c9d6ff24443a",
                "blockNumber": "0x5",
                "from": "0x572f4d80f10f663b5049f789546f25f70bb62a7f",
                "gas": "0x5208",
                "gasPrice": "0x4a817c800",
                "hash": "0xd33150a3f3783f29084eee4e12098f3ef707557f8deb916677a9af68e05613b7",
                "input": "0x",
                "nonce": "0x2",
                "to": "0xef820a678268b3b44f0237abb6739a6d9578b52f",
                "transactionIndex": "0x0",
                "value": "0x2c68af0bb140000",
                "v": "0x150f0",
                "r": "0x82b830674f78f2b518d82e4da67867841bbbeff1968fa07d190138da6a774681",
                "s": "0x1c50991daf54e9426b65a7f3dc958f607189dd07c8131cd9a30ed7c43e3c2df7"
            }
        ],
        "transactionsRoot": "0xac38a6987053157fea9134b9455163d4953d4902c059b8912efcb2733f0b827b",
        "uncles": []
    }
}
```

### Getting a block by number

#### Example Call

```json
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "eth_getBlockByNumber",
    "params": [
        "0x5",
        true
    ],
    "id": 1
}' -H 'Content-Type: application/json' \
   -H 'cache-control: no-cache' \
   127.0.0.1:9650/ext/bc/C/rpc
```

#### Example Response

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "difficulty": "0x1",
        "extraData": "0x64616f2d686172642d666f726b47d8526faa68dca2174ea0a22994d5ca5c1f9ee77a6d6281ba81b0aaf3a972ae",
        "gasLimit": "0x5ee7167",
        "gasUsed": "0x5208",
        "hash": "0x14d9c2aeec20254d966a947e23eb3172ae5067e66fd4e69aecc3c9d6ff24443a",
        "logsBloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
        "miner": "0x0100000000000000000000000000000000000000",
        "mixHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
        "nonce": "0x0000000000000000",
        "number": "0x5",
        "parentHash": "0xc4eb127333754eac38fbd0ef4d036fb6ba39cda0fd3600e2ff91447148f4ef07",
        "receiptsRoot": "0x056b23fbba480696b65fe5a59b8f2148a1299103c4f57df839233af2cf4ca2d2",
        "sha3Uncles": "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
        "size": "0x29e",
        "stateRoot": "0xaf8c1c4dc0eaf6f95ff1a30d0353184e0aa415180bcc32abce9db919f7269961",
        "timestamp": "0x5ed4adf7",
        "totalDifficulty": "0x5",
        "transactions": [
            {
                "blockHash": "0x14d9c2aeec20254d966a947e23eb3172ae5067e66fd4e69aecc3c9d6ff24443a",
                "blockNumber": "0x5",
                "from": "0x572f4d80f10f663b5049f789546f25f70bb62a7f",
                "gas": "0x5208",
                "gasPrice": "0x4a817c800",
                "hash": "0xd33150a3f3783f29084eee4e12098f3ef707557f8deb916677a9af68e05613b7",
                "input": "0x",
                "nonce": "0x2",
                "to": "0xef820a678268b3b44f0237abb6739a6d9578b52f",
                "transactionIndex": "0x0",
                "value": "0x2c68af0bb140000",
                "v": "0x150f0",
                "r": "0x82b830674f78f2b518d82e4da67867841bbbeff1968fa07d190138da6a774681",
                "s": "0x1c50991daf54e9426b65a7f3dc958f607189dd07c8131cd9a30ed7c43e3c2df7"
            }
        ],
        "transactionsRoot": "0xac38a6987053157fea9134b9455163d4953d4902c059b8912efcb2733f0b827b",
        "uncles": []
    }
}
```

### Getting a transaction by hash

#### Example Call

```json
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "eth_getTransactionByHash",
    "params": [
        "0xd33150a3f3783f29084eee4e12098f3ef707557f8deb916677a9af68e05613b7"
    ],
    "id": 1
}' -H 'Content-Type: application/json' \
   -H 'cache-control: no-cache' \
   127.0.0.1:9650/ext/bc/C/rpc
```

#### Example Response

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "blockHash": "0x14d9c2aeec20254d966a947e23eb3172ae5067e66fd4e69aecc3c9d6ff24443a",
        "blockNumber": "0x5",
        "from": "0x572f4d80f10f663b5049f789546f25f70bb62a7f",
        "gas": "0x5208",
        "gasPrice": "0x4a817c800",
        "hash": "0xd33150a3f3783f29084eee4e12098f3ef707557f8deb916677a9af68e05613b7",
        "input": "0x",
        "nonce": "0x2",
        "to": "0xef820a678268b3b44f0237abb6739a6d9578b52f",
        "transactionIndex": "0x0",
        "value": "0x2c68af0bb140000",
        "v": "0x150f0",
        "r": "0x82b830674f78f2b518d82e4da67867841bbbeff1968fa07d190138da6a774681",
        "s": "0x1c50991daf54e9426b65a7f3dc958f607189dd07c8131cd9a30ed7c43e3c2df7"
    }
}
```

### Getting a transaction receipt

#### Example Call

```json
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "eth_getTransactionReceipt",
    "params": [
        "0xd33150a3f3783f29084eee4e12098f3ef707557f8deb916677a9af68e05613b7"
    ],
    "id": 1
}' -H 'Content-Type: application/json' \
   -H 'cache-control: no-cache' \
   127.0.0.1:9650/ext/bc/C/rpc
```

#### Example Response

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "blockHash": "0x14d9c2aeec20254d966a947e23eb3172ae5067e66fd4e69aecc3c9d6ff24443a",
        "blockNumber": "0x5",
        "contractAddress": null,
        "cumulativeGasUsed": "0x5208",
        "from": "0x572f4d80f10f663b5049f789546f25f70bb62a7f",
        "gasUsed": "0x5208",
        "logs": [],
        "logsBloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
        "status": "0x1",
        "to": "0xef820a678268b3b44f0237abb6739a6d9578b52f",
        "transactionHash": "0xd33150a3f3783f29084eee4e12098f3ef707557f8deb916677a9af68e05613b7",
        "transactionIndex": "0x0"}
}
```

### Getting count of pending transactions

"Pending" transactions will be non-zero during periods of heavy network use. "Queued" transactions indicate transactions have been submitted with nonce values ahead of the next expected value for an address, which places them on hold until a transaction with the next expected nonce value is submitted.

#### Example Call

```json
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "txpool_status",
    "params": [],
    "id": 1
}' -H 'Content-Type: application/json' \
   -H 'cache-control: no-cache' \
   127.0.0.1:9650/ext/bc/C/rpc 
```

#### Example Response

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "pending": "0x2f",
        "queued": "0x0"
    }
}
```
