# Tendermint counter example

This example consists of the configuration of two Tendermint nodes running on the same machine.

## Running

### Node 1

Run the application

```
cargo run --release 127.0.0.1:26658
```

Run the Tendermint node

```
TMHOME=./node1 tendermint node
```

### Node 2

Run the application

```
cargo run --release 127.0.0.1:26659
```

Run the Tendermint node

```
TMHOME=./node2 tendermint node
```

## Query

Clients can request either the two nodes 127.0.0.1:26655 and 127.0.0.1:26657.

### Get counter

```
curl '127.0.0.1:26657/abci_query?data='
```

The result should be

```json
{
  "jsonrpc": "2.0",
  "id": -1,
  "result": {
    "response": {
      "code": 0,
      "log": "",
      "info": "",
      "index": "0",
      "key": null,
      "value": "AAAAAAAAAAA=",
      "proof": null,
      "height": "0",
      "codespace": ""
    }
  }
}
```

### Increase counter

The values of consecutive requests should be incremental (1, 2, 3, ...)  

```
curl '127.0.0.1:26657/broadcast_tx_commit?tx=0x01'
```

The result should be

```json
{
  "jsonrpc": "2.0",
  "id": -1,
  "result": {
    "check_tx": {
      "code": 0,
      "data": null,
      "log": "",
      "info": "",
      "gasWanted": "0",
      "gasUsed": "0",
      "events": [],
      "codespace": ""
    },
    "deliver_tx": {
      "code": 0,
      "data": null,
      "log": "",
      "info": "",
      "gasWanted": "0",
      "gasUsed": "0",
      "events": [],
      "codespace": ""
    },
    "hash": "4BF5122F344554C53BDE2EBB8CD2B7E3D1600AD631C385A5D7CCE23C7785459A",
    "height": "17"
  }
}
```

If we pass a non consecutively incremental value, an error should occur:

```json
{
  "jsonrpc": "2.0",
  "id": -1,
  "result": {
    "check_tx": {
      "code": 1,
      "data": null,
      "log": "Count must be incremental!",
      "info": "",
      "gasWanted": "0",
      "gasUsed": "0",
      "events": [],
      "codespace": ""
    },
    "deliver_tx": {
      "code": 0,
      "data": null,
      "log": "",
      "info": "",
      "gasWanted": "0",
      "gasUsed": "0",
      "events": [],
      "codespace": ""
    },
    "hash": "E52D9C508C502347344D8C07AD91CBD6068AFC75FF6292F062A09CA381C89E71",
    "height": "0"
  }
}
```