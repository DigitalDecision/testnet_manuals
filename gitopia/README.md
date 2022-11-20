![на-прозрачном-фоне-png](https://user-images.githubusercontent.com/58205039/202901606-ce094fb7-23d9-40f2-9249-3a029c1d1d5c.png)

# Gitopia - gitopia-janus-testnet-2
____
[Documentation](https://docs.gitopia.com/installation/index.html)
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
apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
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
git clone -b v1.2.0 gitopia://gitopia/gitopia && cd gitopia
make install
```
gitopiad version
+ commit: 64e4712aeae3c723346a365d67cf1dd3e91cc50c
+ version: 1.2.0
### Init
____
```
gitopiad init $MONIKER --chain-id gitopia-janus-testnet-2
```
### Genesis
____
```
wget https://server.gitopia.com/raw/gitopia/testnets/master/gitopia-janus-testnet-2/genesis.json.gz
gunzip genesis.json.gz
mv genesis.json $HOME/.gitopia/config/genesis.json
```
### Configuration
____
```
gitopiad config chain-id quark-1

sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025untrn\"/;" ~/.gitopia/config/app.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.gitopia/config/config.toml

peers="1d3bb209dfc7fe953fb8fa37774bab34016dd75c@185.245.183.85:26656,f0a82f850a0da74c32836b125a52bdfd9a78fdd7@65.108.105.48:11356,ce4d9462b4bb348f1a006faabb40fc4271476463@38.146.3.230:11356,0ae35c02d8b76de9e8af1ec27df2aa446485c774@167.86.94.71:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.gitopia/config/config.toml

seeds="399d4e19186577b04c23296c4f7ecc53e61080cb@seed.gitopia.com:26656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.gitopia/config/config.toml

sed -i 's/max_num_inbound_peers =.*/max_num_inbound_peers = 50/g' $HOME/.gitopia/config/config.toml
sed -i 's/max_num_outbound_peers =.*/max_num_outbound_peers = 50/g' $HOME/.gitopia/config/config.toml

sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.gitopia/config/config.toml

sed -i -e "s/^timeout_commit *=.*/timeout_commit = \"2s\"/" $HOME/.gitopia/config/config.toml
```
### Pruning
____
```
pruning="custom"
pruning_keep_recent="1000"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.gitopia/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.gitopia/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.gitopia/config/app.toml
```
### Indexer 
____
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.gitopia/config/config.toml
```
### Create Service
____
```
sudo tee /etc/systemd/system/gitopiad.service > /dev/null <<EOF
[Unit]
Description=gitopiad
After=network-online.target

[Service]
User=$USER
ExecStart=$(which gitopiad) start
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
systemctl enable gitopiad   
systemctl restart gitopiad && journalctl -u gitopiad -f -o cat
```
