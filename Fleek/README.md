
![Fleek logo](https://user-images.githubusercontent.com/58205039/212378889-b6849104-24a2-4f7b-bd3e-7d834e521eee.png)

# Fleek - KademliaEvent
____
[Documentation](https://blog.fleek.co/posts/fleek-network-node-health-check-guide)
## Hardware Requirements
+ 8x CPUs; the faster clock speed the better
+ 64GB RAM
+ 50Gb of storage (SSD or NVME)

## Tools
____
This line will be added as soon as possible

## Manual
___

### Setup Enviroment
``` bash
apt update && apt upgrade && \
apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```
### Rust
___
```
sudo curl https://sh.rustup.rs -sSf | sh -s -- -y
source $HOME/.cargo/env
```
### Download binaries
___
```
cd $HOME
git clone https://github.com/fleek-network/ursa.git && cd ursa
make install
```

### Create Service
____
```
tee <<EOF >/dev/null /etc/systemd/system/fleekd.service
[Unit]
Description=Fleekd
After=network-online.target

[Service]
User=$USER
ExecStart=/root/.cargo/bin/ursa
WorkingDirectory=$HOME/ursa
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
sudo systemctl daemon-reload && \
sudo systemctl enable fleek && \
sudo systemctl restart fleek && journalctl -n 100 -f -u fleek -o cat
```







  

  
  
  
  
  
