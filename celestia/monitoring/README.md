
![image](https://user-images.githubusercontent.com/58205039/202496987-fe18234c-19eb-4d7f-aa4b-574ff8bcb4c5.png)

____

# Set up monitoring and alerting for celestia validator
## Prerequisites
### Install exporters on validator node
First of all you will have to install exporters on validator node. For that you can use one-liner below
____

```
wget -O install_exporters.sh https://raw.githubusercontent.com/DigitalDecision/testnet_manuals/main/celestia/celestia.sh && chmod +x celestia.sh && ./celestia.sh
```
# Deployment
_____
Monitoring stack needs to be deployed on seperate machine to be able to notify in case if validator goes down! To run monitoring stack you dont need beastly server with multiple cores. It will be more than enough to run it on smallest available vps

## System requirements
Ubuntu 20.04 / 1 VCPU / 2 GB RAM / 20 GB SSD

