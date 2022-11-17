
![image](https://user-images.githubusercontent.com/58205039/202496987-fe18234c-19eb-4d7f-aa4b-574ff8bcb4c5.png)

____

Set up monitoring and alerting for celestia validator
Prerequisites
Install exporters on validator node
First of all you will have to install exporters on validator node. For that you can use one-liner below
____
```
wget -O install_exporters.sh https://raw.githubusercontent.com/kj89/cosmos_node_monitoring/master/install_exporters.sh && chmod +x install_exporters.sh && ./install_exporters.sh
KEY	VALUE
bond_denom	Denominated token name, for example, utia for celestia testnet. You can find it in genesis file
bench_prefix	Prefix for chain addresses, for example, tori for celestia testnet. You can find it in public addresses like this torivaloper1zyyz4m9ytdf60fn9yaafx7uy7h463n7alv2ete
rpc_port	Your validator rpc port that is defined in config.toml file. Default value for celestia is 19657
grpc_port	Your validator grpc port that is defined in app.toml file. Default value for celestia is 19090
make sure following ports are open:

9100 (node-exporter)
9300 (cosmos-exporter)
prometheus metrics should be enabled and port 26660 should be available on validator instance

To enable prometheus you have to run command below and after that please restart service to apply changes

# enable prometheus
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.celestia-app/config/config.toml
Deployment
Monitoring stack needs to be deployed on seperate machine to be able to notify in case if validator goes down! To run monitoring stack you dont need beastly server with multiple cores. It will be more than enough to run it on smallest available vps

System requirements
Ubuntu 20.04 / 1 VCPU / 2 GB RAM / 20 GB SSD

Install monitoring stack
To install monitirng stack you can use one-liner below

wget -O install_monitoring.sh https://raw.githubusercontent.com/kj89/cosmos_node_monitoring/master/install_monitoring.sh && chmod +x install_monitoring.sh && ./install_monitoring.sh
Copy .env.example into .env
sudo cp $HOME/cosmos_node_monitoring/config/.env.example $HOME/cosmos_node_monitoring/config/.env
Update values in .env file
sudo vim $HOME/cosmos_node_monitoring/config/.env
KEY	VALUE
TELEGRAM_ADMIN	Your user id you can get from @userinfobot. The bot will only reply to messages sent from the user. All other messages are dropped and logged on the bot's console
TELEGRAM_TOKEN	Your telegram bot access token you can get from @botfather. To generate new token just follow a few simple steps described here
Export .env file values into .bash_profile
echo "export $(xargs < $HOME/cosmos_node_monitoring/config/.env)" > $HOME/.bash_profile
source $HOME/.bash_profile
Add validator into prometheus configuration file
To add validator use command with specified VALIDATOR_IP, CELESTIA_VALOPER_ADDRESS, CELESTIA_WALLET_ADDRESS and PROJECT_NAME

$HOME/cosmos_node_monitoring/add_validator.sh VALIDATOR_IP CELESTIA_VALOPER_ADDRESS CELESTIA_WALLET_ADDRESS PROJECT_NAME
example: $HOME/cosmos_node_monitoring/add_validator.sh 1.2.3.4 torivaloper1zyyz4m9ytdf60fn9yaafx7uy7h463n7alv2ete tori1zyyz4m9ytdf60fn9yaafx7uy7h463n7a05eshc celestia

To add more validators just run command above with validator values

Run docker compose
Deploy the monitoring stack

cd $HOME/cosmos_node_monitoring
sudo docker compose up -d
ports used:

8080 (alertmanager-bot)
9090 (prometheus)
9093 (alertmanager)
9999 (grafana)
Configuration
Configure Grafana
Open Grafana in your web browser. It should be available on port 9999
image

Login using defaults admin/admin and change password

Import custom dashboard

3.1. Press "+" icon on the left panel and then choose "Import"

image

3.2. Input grafana.com dashboard id 15991 and press "Load"

image

3.3. Select Prometheus data source and press "Import"

image

Congratulations you have successfully configured Cosmos Validator Dashboard
Configrure Telegram alerting
Open conversation with your Telegram bot you created with @botfather and type /start to activate bot
image

Now you are all set! If you want see other commands type /help
If you want learn more about alermanager-bot please visit their github repo

Testing
Test alerts
For simple test you can stop node-exporter service for 5 minutes. It should trigger alert
sudo systemctl stop node_exporter
You will see message from bot firing
image

Now you can start node-exporter service back
sudo systemctl start node_exporter
You will get confirmation from bot that issue is resolved
image

Dashboard contents
Grafana dashboard is devided into 4 sections:

Validator health - main stats for validator health. connected peers and missed blocks
image

Chain health - summary of chain health stats and list of top validators missing blocks
image

Validator stats - information about validator such as rank, bounded tokens, comission, delegations and rewards
image

Hardware health - system hardware metrics. cpu, ram, network usage
image

Cleanup all container data
cd $HOME/cosmos_node_monitoring
sudo docker compose down
sudo docker volume prune -f
