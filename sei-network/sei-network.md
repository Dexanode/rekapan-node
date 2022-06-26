
sei-network testnet GUIDE


## Chain: `sei-testnet-2`
- Official link link is [here](https://github.com/ingenuity-build/testnets/tree/main/killerqueen/kqcosmos-1)
- [Explorer](https://testnet.explorer.testnet.run/kqcosmos-1)
# Instructions
#### Manual Installation
```
sudo apt update
sudo apt install -y make gcc jq curl git
```
#### Install GO
```
if [ ! -f "/usr/local/go/bin/go" ]; then
  . <(curl -s "https://raw.githubusercontent.com/nodejumper-org/cosmos-utils/main/utils/go_install.sh")
  . .bash_profile
fi
go version
```
#### Install **interchain**
```
cd || return
rm -rf sei-chain
git clone https://github.com/sei-protocol/sei-chain.git
cd sei-chain || return
git checkout 1.0.4beta
make install
seid version # 1.0.4beta
```
#### Add variables
# replace aknode dengan moniker anda,
seid config chain-id sei-testnet-2
seid init aknode --chain-id sei-testnet-2 -o
```
curl https://raw.githubusercontent.com/sei-protocol/testnet/master/sei-testnet-2/genesis.json > ~/.sei/config/genesis.json
sha256sum $HOME/.sei/config/genesis.json # aec481191276a4c5ada2c3b86ac6c8aad0cea5c4aa6440314470a2217520e2cc
```
curl https://raw.githubusercontent.com/sei-protocol/testnet/master/sei-testnet-2/addrbook.json > ~/.sei/config/addrbook.json
sha256sum $HOME/.sei/config/addrbook.json # 9058b83fca36c2c09fb2b7c04293382084df0960b4565090c21b65188816ffa6
```
sed -i 's|^minimum-gas-prices *=.*|minimum-gas-prices = "0.0001usei"|g' $HOME/.sei/config/app.toml
seeds=""
peers="f4b1aa3416073a4493de7889505fc19777326825@rpc1-testnet.nodejumper.io:28656"
sed -i -e 's|^seeds *=.*|seeds = "'$seeds'"|; s|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.sei/config/config.toml
```
sed -i 's|pruning = "default"|pruning = "custom"|g' $HOME/.sei/config/app.toml
sed -i 's|pruning-keep-recent = "0"|pruning-keep-recent = "100"|g' $HOME/.sei/config/app.toml
sed -i 's|pruning-interval = "0"|pruning-interval = "10"|g' $HOME/.sei/config/app.toml
```
sudo tee /etc/systemd/system/seid.service > /dev/null << EOF
[Unit]
Description=Sei Protocol Node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which seid) start
Restart=on-failure
RestartSec=10
LimitNOFILE=10000
[Install]
WantedBy=multi-user.target
EOF
```
seid tendermint unsafe-reset-all --home $HOME/.sei --keep-addr-book
```
SNAP_RPC="http://rpc1-testnet.nodejumper.io:28657"
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)
```
Recover wallet created for killerquin-1. Type your saved seed phease.
```
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
```

#### Pruning settings
```
sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.sei/config/config.toml
```
#### Comment out the line `log_level=info`
```
sed -i.bak 's/^log_level/# log_level/' $HOME/.ica/config/config.toml
```
### Сreate a service file
```
sudo tee <<EOF >/dev/null /etc/systemd/system/icad.service
[Unit]
Description=ICA Cosmos daemon
After=network-online.target
[Service]
User=$USER
ExecStart=$(which icad) start --log_level=info
Restart=on-failure
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```
## Start the service to synchronize from first block
```
sudo systemctl daemon-reload
sudo systemctl enable seid
sudo systemctl restart seid
```
### Logs and status
Logs
```
sudo journalctl -u seid -f --no-hostname -o cat
```