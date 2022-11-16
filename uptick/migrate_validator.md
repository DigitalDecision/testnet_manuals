1. Run a new full node on a new machine
To setup full node you can follow my guide uptick node setup for Testnet

2. Confirm that you have the recovery seed phrase information for the active key running on the old machine
To backup your key
uptickd keys export mykey
This prints the private key that you can then paste into the file mykey.backup

To get list of keys
uptickd keys list
3. Recover the active key of the old machine on the new machine
This can be done with the mnemonics
uptickd keys add mykey --recover
Or with the backup file mykey.backup from the previous step
uptickd keys import mykey mykey.backup
4. Wait for the new full node on the new machine to finish catching-up
To check synchronization status
uptickd status 2>&1 | jq .SyncInfo
catching_up should be equal to false

5. After the new node has caught-up, stop the validator node
To prevent double signing, you should stop the validator node before stopping the new full node to ensure the new node is at a greater block height than the validator node If the new node is behind the old validator node, then you may double-sign blocks

Stop and disable service on old machine
sudo systemctl stop uptickd
sudo systemctl disable uptickd
The validator should start missing blocks at this point

6. Stop service on new machine
sudo systemctl stop uptickd
7. Move the validator's private key from the old machine to the new machine
Private key is located in: ~/.uptickd/config/priv_validator_key.json
After being copied, the key priv_validator_key.json should then be removed from the old node's config directory to prevent double-signing if the node were to start back up

sudo mv ~/.uptickd/config/priv_validator_key.json ~/.uptickd/bak_priv_validator_key.json
8. Start service on a new validator node
sudo systemctl start uptickd
The new node should start signing blocks once caught-up

9. Make sure your validator is not jailed
To unjail your validator
uptickd tx slashing unjail --chain-id $UPTICK_CHAIN_ID --from mykey --gas=auto -y
10. After you ensure your validator is producing blocks and is healthy you can shut down old validator server
