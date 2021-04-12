# 2 Nodes Ethereum Private Blockchain

This is a **ready to use** private blockchain of 2 nodes. You can choose between a Proof of Work algorithm (default dificulty 0x100 == 256) or a Proof of Authority algorithm (default time between blocks = 5 seconds).

You can change those parameters in the genesis files (`.\gethblockchainPOA.json` and `.\gethblockchainPOW.json`)

I configured both of them with 2 accounts in each node, the first one is full of Ether, the other is empty. Don't use this accounts in a real network with real Ether ;) There are also other accounts in the network with Eth that I used myself.

The first accounts of both nodes (the ones with funds) have their password stored in a file `password.txt` in their folder. This is a password to decipher the file in the `keystore/` folder, which contains the private key that signs transactions to submit them to the blockchain.

It's included there to use an all-in-one command later, for secutiry reasons it could be stored elsewhere.

Check the accounts section for more info about the accounts in each node.

There's also a `bootnode`, this is a node that doesn't hold a copy of the blockchain, it is part of the discovery protocol between peers.

## Run it :muscle:

We are using ports 8001, 8002, 8011, 8012, 30310, 30311, 30312. If you have some of them occupied, just change them in the commands.

First we need to set the Genesis block (i.e. the first block of the blockchain, which defines things like the consensus algorithms, prefunded accounts...).

Open a terminal for each node and run all the commands for the node in the same terminal. Only run the commands for a particular Consensus Algorithm:

For Proof of Work:

- `Geth --datadir .\node1\ init .\gethblockchainPOW.json`
- `Geth --datadir .\node2\ init .\gethblockchainPOW.json`

For Proof of Authority:

- `Geth --datadir .\node1\ init .\gethblockchainPOA.json`
- `Geth --datadir .\node2\ init .\gethblockchainPOA.json`

Now the nodes have their genesis files, good job! You may notice there's a folder `geth/` in your node folder. This is the folder that stores the copy of the blockchain. If you modify the genesis file or want to use another consensus algorithm, after stopping the nodes you will need to delete `geth/` folder in both nodes and repeat the commands above.

Now, run in a third terminal the command to launch the bootnode. This node uses the key stored in boot.key, if you use another one, the bootnode will have a different enode address that you may modify in the latest pair of commands. You can change the verbosity flag if it gets annoying watching PINGs and PONGs between nodes.

- `bootnode -nodekey boot.key -verbosity 9 -addr :30310`

It's time to run the nodes. Run one command in each terminal, it will stay there showing logs.
The command is really simmilar for both consensus algorithms, but with one difference. I configured the PoW network with an id of 101 and the PoA network with an id of 102, in order to differentiate them.
Please take in mind when copying and pasting the command that you may just put 101 or 102 instead of **[101|102]**

Command for node 1:

- `geth --identity "node1" --syncmode 'full' --rpc --rpcport "8001" --rpccorsdomain "*" --datadir .\node1\ --port "30301" --rpcapi "db,eth,net,web3,personal,miner,admin" --networkid `**[101|102]**` --nat "any" --ipcdisable --allow-insecure-unlock --bootnodes 'enode://2fedd17a9a11202649eae7981764fc84eeffbdce68571206ee9ce3a670dea1a212f2e2b9bfdef22aa25c43c9ffc8bed6a657bff004ab17a915faeb143cc078dc@127.0.0.1:0?discport=30310' -unlock '0xdbA9A8E631585FdF48c511C25999f840473Ca6A4' --password node1/password.txt --mine --ws --ws.addr 'localhost' --ws.port "8011" --ws.api "db,eth,net,web3,personal,miner,admin" --ws.origins="*"`

Command for node 2:

- `geth --identity "node2" --syncmode 'full' --rpc --rpcport "8002" --rpccorsdomain "*" --datadir .\node2\ --port "30302" --rpcapi "db,eth,net,web3,personal,miner,admin" --networkid `**[101|102]**` --nat "any" --ipcdisable --allow-insecure-unlock --bootnodes 'enode://2fedd17a9a11202649eae7981764fc84eeffbdce68571206ee9ce3a670dea1a212f2e2b9bfdef22aa25c43c9ffc8bed6a657bff004ab17a915faeb143cc078dc@127.0.0.1:0?discport=30310' -unlock '0x4c4c1C9B0F8952fff26a3824f2b850A6D19E6503' --password node2/password.txt --mine --ws --ws.addr 'localhost' --ws.port "8012" --ws.api "db,eth,net,web3,personal,miner,admin" --ws.origins="*"`

For each node: we are allowing discovery between the nodes via the bootnode, we are exposing the API via HTTP RCP and WebSockets, we are unlocking the first account (the one with funds), setting it as miner (we need the password for that).

For security reasons, you may not want to expose all the API via RPC and WS, allow every connection, unlock the account in the same command... But this is a playground, so feel free to use it like this.

It's important to expose both APIs since different tools talk to the blockchain differently. For example, Truffle will deploy a contract using RPC and Hyperledger Caliper Benchmark will only submit transactions using websockets.

You may open a javascript console to execute commands in each node using both http or ws, just make sure you use the correct port:

Node 1:

- `geth attach http://localhost:8001`
- `geth attach http://localhost:8002`

Node 2:

- `geth attach ws://localhost:8011`
- `geth attach ws://localhost:8012`

Using this console you can, for example, send a transaction from the account with funds to the acount without funds in this node.

## Accounts :dollar:

### Node 1

Account 1 (prefunded):

- **Public:** `0xdbA9A8E631585FdF48c511C25999f840473Ca6A4`
- **Path secret key file:** `node1\keystore\UTC--2021-03-08T09-59-33.- 466696300Z--dba9a8e631585fdf48c511c25999f840473ca6a4`
- **Password:** `password1`

Account 2 (no funds):

- **Public:** `0xF9076Fdd31eA41abDE05008241D2A62Fa7167a1E`
- **Path secret key file:** `node1\keystore\UTC--2021-03-08T11-09-32.996981000Z--f9076fdd31ea41abde05008241d2a62fa7167a1e`
- **Password:** `pass`

### Node 2

Account 1 (prefunded):

- **Public:** `0x4c4c1C9B0F8952fff26a3824f2b850A6D19E6503`
- **Path secret key file:** `node2\keystore\UTC--2021-03-08T10-01-11.651636000Z--4c4c1c9b0f8952fff26a3824f2b850a6d19e6503`
- **Password:** `password2`

Account 2 (no funds):

- **Public:** `0x821820B62F6387DBde2603935a98b948b324ccAD`
- **Path secret key file:** `node2\keystore\UTC--2021-03-08T11-10-52.533171200Z--821820b62f6387dbde2603935a98b948b324ccad`
- **Password:** `pass`
