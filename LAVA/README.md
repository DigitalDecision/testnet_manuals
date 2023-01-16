![image](https://user-images.githubusercontent.com/58205039/212494991-f94f5c2b-fd0a-4547-8f3a-3f2903b8bc46.png)

# Lava - lava-testnet - 1
____
[Documentation](https://docs.okp4.network/nodes/run-node)
## Hardware Requirements
+ 8x CPUs; the faster clock speed the better
+ 64GB RAM
+ 1TB of storage (SSD or NVME)

## Tools
____

## Manual
___

### Setup Enviroment
``` bash
apt update && apt upgrade && \
apt install curl iptables build-essential git wget jq make cmake gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```
### Go
___
```
ver="1.19.1" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```
### Download binaries
___
```
git clone https://github.com/lavanet/lava && cd lava
git checkout v0.4.3
make install
```
```
lavad version --long | grep -e version -e commit
```
+ 0.4.3
+ commit: 5673a81fc2eb18f64d1842d0c076771b55a7487d
### Init
____
```
lavad init $MONIKER --chain-id lava-testnet-1
```
### Genesis
____
```
cd $HOME
git clone https://github.com/K433QLtr6RA9ExEq/GHFkqmTzpdNLDd6T
cp $HOME/GHFkqmTzpdNLDd6T/testnet-1/genesis_json/genesis.json $HOME/.lava/config
```
```
sha256sum ~/.lava/config/genesis.json
```
+ 72170a8a7314cb79bc57a60c1b920e26457769667ce5c2ff0595b342c0080d78\


### AddrBook
____
```
wget -O $HOME/.lava/config/addrbook.json "https://share2.utsa.tech/lava/addrbook.json"
```

### Configuration
____
```
lavad config chain-id lava-testnet-1

lavad config keyring-backend os

sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0025ulava\"/;" ~/.lava/config/app.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.lava/config/config.toml

peers=""
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.lava/config/config.toml

seeds="3a445bfdbe2d0c8ee82461633aa3af31bc2b4dc0@prod-pnet-seed-node.lavanet.xyz:26656,e593c7a9ca61f5616119d6beb5bd8ef5dd28d62d@prod-pnet-seed-node2.lavanet.xyz:26656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.lava/config/config.toml

sed -i 's/max_num_inbound_peers =.*/max_num_inbound_peers = 50/g' $HOME/.lava/config/config.toml
sed -i 's/max_num_outbound_peers =.*/max_num_outbound_peers = 25/g' $HOME/.lava/config/config.toml

sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.lava/config/config.toml

sed -i -e "s/^timeout_commit *=.*/timeout_commit = \"60s\"/" $HOME/.lava/config/config.toml
sed -i -e "s/^timeout_propose *=.*/timeout_propose = \"60s\"/" $HOME/.lava/config/config.toml
sed -i -e "s/^create_empty_blocks_interval *=.*/create_empty_blocks_interval = \"60s\"/" $HOME/.lava/config/config.toml

```
### Pruning
____
```
pruning="custom"
pruning_keep_recent="1000"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.lava/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.lava/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.lava/config/app.toml
```
### Indexer 
____
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.lava/config/config.toml
```
### Create Service
____
```
tee /etc/systemd/system/lavad.service > /dev/null <<EOF
[Unit]
Description=lavad
After=network-online.target

[Service]
User=$USER
ExecStart=$(which lavad) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
### Start service
____
```
systemctl daemon-reload
systemctl enable lavad
systemctl restart lavad && journalctl -u lavad -f -o cat
```
