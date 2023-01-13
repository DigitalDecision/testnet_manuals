

sudo apt update
sudo apt install mc wget curl git htop net-tools unzip jq build-essential ncdu tmux make cmake clang pkg-config libssl-dev protobuf-compiler -y


sudo curl https://sh.rustup.rs -sSf | sh -s -- -y
source $HOME/.cargo/env


 cd $HOME
 git clone https://github.com/fleek-network/ursa.git && cd ursa
 make install
  
  
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
  
  
  
  sudo systemctl daemon-reload && \
  sudo systemctl enable fleek && \
  sudo systemctl restart fleek && journalctl -n 100 -f -u fleek -o cat
  
