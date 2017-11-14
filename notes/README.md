# Introduction

## Start-up nodes

### Launch instances

To launch Ethereum nodes on AWS, follow the steps below.

1. Launch an AWS EC2 `t2.micro` instance of Ubuntu 64-bit OS.
1. For now, launch the instance in the default VPC and allow it to have a public IP address.
1. Once launched, note the IP address.

Repeat steps 1 to 3 to start one more instance.

### Install Ethereum client and genesis file

1. Log onto an instance with `ssh`.
1. Install `go-ethereum` client on this machine with instructions below.
1. Create a directory by name `eth-priv`.
1. Create a file in the `eth-priv` named as `genesis.json` as shown below.

```bash
sudo apt-get install software-properties-common
sudo add-apt-repository -y ppa:ethereum/ethereum
sudo apt-get update
sudo apt-get install ethereum
```

```json
{
    "config": {
        "chainId": 15,
        "homesteadBlock": 0,
        "eip155Block": 0,
        "eip158Block": 0
    },
    "nonce": "0x0000000000000042",
    "mixhash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "difficulty": "0x4000",
    "alloc": {},
    "coinbase": "0x0000000000000000000000000000000000000000",
    "timestamp": "0x00",
    "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "gasLimit": "0xffffffff"
}
```

NOTES:
The `genesis.json` shown above has

* `chainId` set to `15` to differentiate itself as a private Ethereum network.
* `gasLimit` is set to maximum.
* `difficulty` is set to low.

### Initialization

Follow the steps to start Ethereum on **first** machines.

1. Initialize

```bash
geth --data-dir eth-priv init eth-priv/genesis.json
```

1. Start blockchain with the command below. Note that, the value of `--networkid` matches with the value of `chainId` in `genesis.json` file. Also, with the `--nodiscover` flag, this machine becomes a sort of host machine. Finally, this machine will listen of peering connections at port number `30333`.

```bash
geth --datadir "/home/ubuntu/eth-priv" --networkid 15 --nodiscover --maxpeers 3  --port 30333 console
```

1. From the console, get the node value.

```bash
admin.nodeInfo.enode
"enode://342a11d352151b3dfeb78db02a4319e1255c9fb49bc9a1dc44485f7c1bca9cc638540833e4577016f9a6180d1e911d907280af9b3892c53120e1e30619594eba@[::]:30333?discport=0"
```

1. Exit console with a `Ctrl-D`.

### Add peers

1. Log on to second machine, create folder `eth-priv` and copy `genesis.json` (created above) here. Start Ethereum with command below.

```bash
geth --datadir "/home/ubuntu/eth-priv" --networkid 15 --maxpeers 2  --port 30333 console
```

1. From the console, add peer.

To add a peer, replace the `[::]` in the node information value (see step 3 in previous section) of the first machine with the public IP address of the first machine.

```bash
admin.addPeer("enode://342a11d352151b3dfeb78db02a4319e1255c9fb49bc9a1dc44485f7c1bca9cc638540833e4577016f9a6180d1e911d907280af9b3892c53120e1e30619594eba@18.0.0.0:30333?discport=0")
```

Repeat the above steps on the third machine.

On the fourth machine, add a `static-nodes.json` file in the `eth-priv` folder with the node information of the first machine. For example,

```json
[
    "enode:///342a11d352151b3dfeb78db02a4319e1255c9fb49bc9a1dc44485f7c1bca9cc638540833e4577016f9a6180d1e911d907280af9b3892c53120e1e30619594eba@18.0.0.0:30333"
]

### Start the network

1. Log onto first machine and start Ethereum with the command below.

```bash
geth --datadir "/home/ubuntu/eth-priv" --networkid 15 --nodiscover --maxpeers 2  --port 30333 console
```

1. Log onto second machine and start Ethereum with the command below.

```bash
geth --datadir "/home/ubuntu/eth-priv" --networkid 15 --port 30333 console
```

1. Check for connected peer with the command below.

```bash
admin.peers
```

1. Exit with `Ctrl-D` on the third machine.

### Set-up accounts

For now, we will set-up accounts only on the third machine. First, we launch the console as shown below.

```bash
geth --datadir "/home/ubuntu/eth-priv" --networkid 15 console
```

Then, we add an account as below.

```bash
geth --datadir "/home/ubuntu/eth-priv" account new
```

Finally, we start the mining process so that ethers are credited to this account.

```bash
geth --datadir "/home/ubuntu/eth-priv" --networkid 15 --mine
```

We can check the balance using the following command on the console.

```bash
eth.getBalance(eth.accounts[0])
```

### Start RPC

On the third machine, open up the RPC port to allow for communication with a client.

```bash
geth --datadir eth-priv --networkid 15 --maxpeers 2 --port 30333 --rpc --rpcapi "web3,eth" --rpcaddr "0.0.0.0" --rpccorsdomain "*"
```

**NOTE** that, the `--rpcaddr` value has been setting for testing only. This value is **discouraged**.

## Mining

## End-point

## Conclusion