# meteor-test (TEST NET) Nov 08, 2022 Chain Halt Upgrade

RCA and reason for this upgrade:
1. We took a decision of updating our nodes with the latest go version due to few recent incidents.
2. After updating all 4 nodes with the latest go version binary post 25 blocks we started getting apphash errors on all nodes, the strange thing we noticed here was that the expected and got hash were same on all the nodes.
3. We tried to restore the nodes with foundation and Zenchainlab's snapshot and wasm directory but got apphash again while the nodes were syncing.
4. There were no errors reported at the block, and we suspect that it was the issue with wasm data which were the root cause where other validators faced issue to sync and start the chain in past on our testnet. Using the snapshot with correct wasm directory used to work but in yesterday's case it didn't as all our nodes had the major voting power, and it was tricky to get the chain back up. 
5. And hence we took below decision to restart the test net

This upgrade is effectively a hard fork with the same chain-id. This means we'll need to delete all previous data and start from a new genesis.

## Performing the Upgrade

### 1. Stop the node
```sh
sudo systemctl stop comdex
```

### 2. Backup priv_validator_key.json
This is only relevant if you're upgrading your validator. Your priv_validator_key.json is how your validator is identified. If you haven't backed it up already, **DO SO NOW**.

An example method for doing so is as follows, which will copy your validator key to the home dir:
```sh
cd ~
cp ~/.comdex/config/priv_validator_key.json .
```

*NOTE*: keeping your copy of the validator key on the same machine is NOT sufficient. At a bare minimum it should be backed up locally so you always have access to it.

### 3. Backup priv_validator_state.json
It's worth backing up your `priv_validator_state.json` in case this upgrade fails and an alternative path must be taken.
```sh
cd ~
cp ~/.comdex/data/priv_validator_state.json .
```

### 4. Purge previous chain state and addrbook.json
Because we'll be starting from a new genesis, the previous data is no longer necessary.
```sh
comdex tendermint unsafe-reset-all --home $HOME/.comdex
```

### 5. Purge current peers and seeds from config.toml
This is done to ensure all peers are clean when moving forward. The addrbook.json file was already purged in the previous step.

**WARNING:** DO NOT DO BLANK PERSISTENT PEERS IF YOU ARE RUNNING SENTRIES. Only remove persistent peers that are not your sentries/val node.

```sh
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"\"/" ~/.comdex/config/config.toml
sed -i.bak -e "s/^seeds *=.*/seeds = \"\"/" ~/.comdex/config/config.toml
```

### 6. Add seeds and peers to config.toml
These are all verified to be using the new genesis file and binary.

**WARNING:** These should be added manually if you are running a sentries setup, or you will blank out your peers.
```sh
SEEDS=""
PEERS=""
sed -i.bak -e "s/^seeds *=.*/seeds = \"$SEEDS\"/" ~/.comdex/config/config.toml
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" ~/.comdex/config/config.toml
```

### 7. Download and install the new binary

#### 7a. Install comdex v5.0.0.beta
This new binary includes the fix to export the genesis,there was a bug which blocked the export.
```sh
git clone https://github.com/comdex-official/comdex
cd comdex
git fetch
git checkout v5.0.0.beta
make build && make install
```

To confirm the correct binary is installed, do:
```sh
codmex version --long
```

```sh
name: comdex
server_name: comdex
version: v5.0.0.beta
commit: 
build_tags: netgo,ledger
go: go version go1.19.3 linux/amd64
```

#### 7c. [OPTIONAL] If you use cosmovisor
You will need to re-setup cosmovisor with the new genesis.
```sh
rm $DAEMON_HOME/cosmovisor/genesis/bin/comdex
rm -rf $DAEMON_HOME/cosmovisor/upgrades
mkdir $DAEMON_HOME/cosmovisor/upgrades
cp $HOME/go/bin/comdex $DAEMON_HOME/cosmovisor/genesis/bin
rm $DAEMON_HOME/cosmovisor/current
```

Check comdex has copied to the new location.
```sh
$DAEMON_HOME/cosmovisor/genesis/bin/comdex version

# returns
v5.0.0.beta

tree $DAEMON_HOME/cosmovisor

# returns
$HOME.comdex/cosmovisor
├── genesis
│   └── bin
│       └── comdex
└── upgrades
```

### 8. Download the new genesis

To build yourself or check options, [read more here](./genesis.md).

```sh
rm ~/.comdex/config/genesis.json
wget 
tar -xvf 
mv meteor-new-genesis.json $HOME/.comdex/config/genesis.json

# check chain is meteor-test, genesis time is correct & initial block is 
# note if using zsh that you may need to break this up, and run steps individually
# i.e. cat $HOME/comdex/config/genesis.json | jq '.chain_id'
cat $HOME/.comdex/config/genesis.json | jq '"Genesis Time: " + .genesis_time + " — Chain ID: " + .chain_id + " - Initial Height: " + .initial_height'
```

### 9. Verify genesis shasum

```sh
jq -S -c -M '' ~/.comdex/config/genesis.json | sha256sum

# this will return
#   - ffffff-
```

### 10. Double check
This isn't strictly necessary - you can skip it. However, you might want to be paranoid and just double-check.
```sh
comdex tendermint unsafe-reset-all --home $HOME/.comdex
```

### 11. Restore priv_validator_key.json and priv_validator_state.json

**Important** By resetting the state we also resetted the validator sign state, which may cause double sign. We need to restore our backup to prevent this.

If you are using a remote signer this step is probably not needed

```sh
cp ~/priv_validator_state.json ~/.comdex/data/priv_validator_state.json
cp ~/priv_validator_key.json ~/.comdex/config/priv_validator_key.json
```

### 12. Start the node
```sh
sudo systemctl restart comdex
```

### 13. Confirm the process running
```sh
sudo journalctl -fu comdex
```

**The node will take roughly 60 minutes to start**, with 32GB of RAM. After it's finished starting, the output should be as follows:

