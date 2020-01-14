# Chainlink Bitcoin External Adapter

Adapter that connects Chainlink oracle nodes to the bitcoin network faciliating BTC JSON-RPC calls.

This allows Chainlink oracles to return information about BTC transactions and wallet addresses.

Supported RPC commands:

- ✅ getblockcount
- ✅ getdifficulty
- ❌ getrawtransaction
- ❌ getblock
- ❌ searchrawtransactions

Supported BTC client backends:

- ✅ btcd
- ❌ bitcoind

### TODO

- Add support for other BTC clients (ex: bitcoind)

# Prerequisites

Make sure you have a btcd instance sync'd on mainnet and the RPC enabled. You an get btcd here:
https://github.com/btcsuite/btcd

To enable the RPC, ensure there's a `rpcuser` and `rpcpassword` defined in your `btcd.conf` file.

You will also need a copy of `rpc.cert` from the btcd instance to be specified with the `BTCD_RPC_CERT` environment variable when running the adapter.

Finally, make sure the instance of btcd you're sending RPC requests is sync'd with the following configuration options enabled:

```
txindex=1
addrindex=1
```

# Contract Usage

To use this adapter on-chain, find a node that supports this adapter and build your request like so:

```
Chainlink.Request memory req = buildChainlinkRequest(jobId, this, this.fulfill.selector);
run.add("rpc_command", "getBlockCount");
string[] memory copyPath = new string[](1);
copyPath[0] = "block_count";
```

# Setup Instructions

## Local Install

Make sure [Golang](https://golang.org/pkg/) is installed.

Build:

```
make build
```

Then run the adapter:

```
BTCD_RPC_HOST=127.0.0.1:8334 BTCD_RPC_USER=username BTCD_RPC_PASS=password BTCD_RPC_CERT=./rpc.cert ./chainlink-adapter-btcd
```

## Docker

To run the container (note: make sure to escape special characters with a leading `\`):

```
docker run -it -e BTCD_RPC_HOST=127.0.0.1:8334 -e BTCD_RPC_USER=username -e BTCD_RPC_PASS=password -e BTCD_RPC_CERT=./rpc.cert -p 8080:8080 acoutts/chainlink-bitcoin-adapter
```

## Usage Example (curl)

Note: the rpc_command is case-insensitive.

```
curl -X POST -H 'Content-Type: application/json' \
-d @- << EOF
{
	"jobRunId": "1234",
	"data": {
		"rpc_command": "getBlockCount"
	}
}
EOF
```

## REST Response Example:

```json
{
  "jobRunId": "1234",
  "status": "completed",
  "error": null,
  "pending": false,
  "data": {
    "block_count": 612699,
    "rpc_command": "getBlockCount"
  }
}
```

# RPC Reference

Below is an overview of the supported RPC commands and their responses.

# getBlockCount

Returns the number of blocks in the longest block chain.

### Request Data:

```
"data": {
  "rpc_command": "getBlockCount"
}
```

### Solidity Example

```
Chainlink.Request memory req = buildChainlinkRequest(jobId, this, this.fulfill.selector);
run.add("rpc_command", "getBlockCount");
string[] memory copyPath = new string[](1);
copyPath[0] = "block_count";
```

### Response Success

```json
{
  "jobRunId": "1234",
  "status": "completed",
  "error": null,
  "pending": false,
  "data": {
    "block_count": 612699,
    "rpc_command": "getBlockCount"
  }
}
```

### Response Error

```json
{
  "jobRunId": "1234",
  "status": "errored",
  "error": "Unable to connect to btcd instance",
  "pending": false,
  "data": {
    "rpc_command": "getBlockCount"
  }
}
```

# getDifficulty

Returns the proof-of-work difficulty as a multiple of the minimum difficulty.

### Request Data:

```
"data": {
  "rpc_command": "getDifficulty"
}
```

### Solidity Example

```
Chainlink.Request memory req = buildChainlinkRequest(jobId, this, this.fulfill.selector);
run.add("rpc_command", "getDifficulty");
string[] memory copyPath = new string[](1);
copyPath[0] = "difficulty";
```

### Response Success

```json
{
  "jobRunId": "1234",
  "status": "completed",
  "error": null,
  "pending": false,
  "data": {
    "difficulty": 13798783827516.416,
    "rpc_command": "getDifficulty"
  }
}
```

### Response Error

```json
{
  "jobRunId": "1234",
  "status": "errored",
  "error": "Unable to connect to btcd instance",
  "pending": false,
  "data": {
    "rpc_command": "getDifficulty"
  }
}
```
