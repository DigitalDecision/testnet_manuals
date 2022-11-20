
![Okp4_logo](https://user-images.githubusercontent.com/58205039/202905030-7e03f289-d75c-4f19-afa7-ff744e96ee5a.png)

# OKP4 - okp4-nemeton
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
cd $HOME
git clone https://github.com/okp4/okp4d.git
cd okp4d
git checkout v2.2.0
make install
```
okp4d version
+ commit: 64e4712aeae3c723346a365d67cf1dd3e91cc50c
+ version: 1.2.0
### Init
____
```
okp4d init $MONIKER --chain-id okp4-nemeton
```
### Genesis
____
```
wget -qO $HOME/.okp4d/config/genesis.json "https://raw.githubusercontent.com/okp4/networks/main/chains/nemeton/genesis.json"
```
### Configuration
____
```
okp4d config chain-id okp4-nemeton

sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025untrn\"/;" ~/.okp4d/config/app.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.okp4d/config/config.toml

peers=""
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.okp4d/config/config.toml

seeds="8e1590558d8fede2f8c9405b7ef550ff455ce842@51.79.30.9:26656,bfffaf3b2c38292bd0aa2a3efe59f210f49b5793@51.91.208.71:26656,106c6974096ca8224f20a85396155979dbd2fb09@198.244.141.176:26656,a7f1dcf7441761b0e0e1f8c6fdc79d3904c22c01@38.242.150.63:36656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.okp4d/config/config.toml

sed -i 's/max_num_inbound_peers =.*/max_num_inbound_peers = 50/g' $HOME/.okp4d/config/config.toml
sed -i 's/max_num_outbound_peers =.*/max_num_outbound_peers = 50/g' $HOME/.okp4d/config/config.toml

sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.okp4d/config/config.toml

sed -i -e "s/^timeout_commit *=.*/timeout_commit = \"2s\"/" $HOME/.okp4d/config/config.toml
```
### Pruning
____
```
pruning="custom"
pruning_keep_recent="1000"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.okp4d/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.okp4d/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.okp4d/config/app.toml
```
### Indexer 
____
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.okp4d/config/config.toml
```
### Create Service
____
```
sudo tee /etc/systemd/system/okp4d.service > /dev/null <<EOF
[Unit]
Description=okp4d
After=network-online.target

[Service]
User=$USER
ExecStart=$(which okp4d) start
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
systemctl enable okp4d   
systemctl restart okp4d && journalctl -u okp4d -f -o cat
```
