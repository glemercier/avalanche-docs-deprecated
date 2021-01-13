# APIs

Clients interact with Avalanche through APIs calls to nodes.

Numeric parameters in API calls may be given as strings (e.g. `"5"` or `5` are both ok for an integer argument).
Numeric return values are always given as strings (e.g. `"5"` rather than `5`).

In examples, API calls are made to a node listening for HTTP traffic on `127.0.0.1:9650`.

## [AVM (X-Chain) API](./avm.md)

The X-Chain, Avalanche's native platform for creating and trading assets, is an instance of the Avalanche Virtual Machine (AVM).
This API allows clients to create and trade assets, including AVAX, on the X-Chain as well as other instances of the AVM.

## [EVM (C-Chain) API](./evm.md)

Allows clients to interact with the C-Chain, Avalanche's main EVM instance, as well as other EVM instances.

## [P-Chain API](./platform.md)

Allows clients to interact with the P-Chain (Platform Chain), which maintains Avalanche's validator set and handles blockchain and subnet creation.

## [Keystore API](./keystore.md) 

Allows clients to use an Avalanche node's built-in keystore.

## [Health API](./health.md)

Allows client to check a node's health.

## [Metrics API](./metrics.md) 

Allows clients to collect Prometheus metrics about a node.

## [Admin API](./admin.md)

Allows clients to examine a node's internal state, set of connections, and similar internal protocol data.

## [Info API](./info.md)

Allows clients to examine basic information about a node.
