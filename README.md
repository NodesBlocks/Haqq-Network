# Haqq Installation Guide

## Minimum Hardware Requirements

- 3x CPU; the higher the clock speed the better
- 4GB of RAM
- 80GB Disk
- Persistent Internet connection (traffic will be minimum 10Mbps during testnet - at least 100Mbps expected for production)

## Recommended Hardware Requirements

- 4x CPU; the higher the clock speed the better
- 8GB of RAM
- 200 GB storage (SSD or NVME)
- Persistent Internet connection (traffic will be minimum 10Mbps during testnet - at least 100Mbps expected for production)

# Haqq Full Node Installation Steps

## Automatic Installation with a Single Script

You can set up your Haqq fullnode in a few minutes using the automated script below. You will be asked for your node name (NODENAME) during the script!

```bash
wget -O HAQQ.sh https://raw.githubusercontent.com/NodesBlocks/Haqq-Network/main/HAQQ && chmod +x HAQQ.sh && ./HAQQ.sh
```
## Post-Installation Steps

You should make sure your validator syncs blocks. You can use the following command to check the sync status.
```bash
haqqd status 2>&1 | jq .SyncInfo
```
## Recover a Wallet

To recover your wallet using mnemonic:
```bash
haqqd keys add $WALLETNAME --recover
```
To get the current wallet list
```bash
haqqd keys list
```
## Save Wallet Information

Add Wallet Address:
```bash
WALLET_ADDRESS=$(haqqd keys show $WALLETNAME -a)
VALOPER_ADDRESS=$(haqqd keys show $WALLETNAME --bech val -a)
echo 'export WALLET_ADDRESS='${WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export VALOPER_ADDRESS='${VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```
## Edit validator

Before creating a validator please make sure you have at least 1 ISLM (1 ISLM equals 1000000 aISLM) and your node is in sync.

To check your wallet balance
```bash
haqqd query bank balances $WALLETNAME
```
If you can't see your balance in your wallet, chances are your node is still syncing. Please wait for the sync to finish and then continue

Edit a Validator:
```bash
haqqd tx staking edit-validator \
  --details "Details about your validator" \
  --website "Your website" \
  --identity "You can find it on keybase.io" \
  --security-contact "Your e-mail" \
  --moniker $NODENAME \
  --chain-id $CHAIN_ID \
  --fees 250aISLM
```
# Useful Commands

***Service Management***

Check Logs:
```bash
journalctl -fu haqqd -o cat
```
Start Service:
```bash
systemctl start haqqd
```
Stop Service:
```bash
systemctl stop haqqd
```
Restart Service:
```bash
systemctl restart haqqd
```
***Node Information***

Sync Information:
```bash
haqqd status 2>&1 | jq .SyncInfo
```
Validator Information:
```bash
haqqd status 2>&1 | jq .ValidatorInfo
```
Node Information:
```bash
haqqd status 2>&1 | jq .NodeInfo
```
Show Node ID:
```bash
haqqd tendermint show-node-id
```
***Wallet Transactions***

List of Wallets:
```bash
haqqd keys list
```
Recover wallet using Mnemonic:
```bash
haqqd keys add $WALLETNAME --recover
```
Wallet Delete:
```bash
haqqd keys delete $WALLETNAME
```
Show Wallet Balance:
```bash
haqqd query bank balances $WALLETNAME
```
Transfer tokens between wallets:
```bash
haqqd tx bank send $WALLETNAME <TO_WALLET_ADDRESS> 10000000aISLM
```
***Voting***
```bash
haqqd tx gov vote 1 yes --from $WALLETNAME --chain-id=$CHAIN_ID
```
***Stake, Delegation and Rewards***

Delegate Process:
```bash
haqqd tx staking delegate $VALOPER_ADDRESS 10000000aISLM --from=$WALLETNAME --chain-id=$CHAIN_ID --gas=auto --fees 250aISLM
```
Redelegate from validator to another validator:
```bash
haqqd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 10000000aISLM --from=$WALLETNAME --chain-id=$CHAIN_ID --gas=auto --fees 250aISLM
```
Withdraw all rewards:
```bash
haqqd tx distribution withdraw-all-rewards --from=$WALLETNAME --chain-id=$CHAIN_ID --gas=auto --fees 250aISLM
```
Withdraw rewards with commission:
```bash
haqqd tx distribution withdraw-rewards $VALOPER_ADDRESS --from=$WALLETNAME --commission --chain-id=$CHAIN_ID
```
Get Out Of Jail(Unjail):
```bash
haqqd tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLETNAME \
  --chain-id=$CHAIN_ID \
  --gas=auto --fees 250aISLM
```
To Delete Node Completely:
```bash
sudo systemctl stop haqqd
sudo systemctl disable haqqd
sudo rm /etc/systemd/system/haqqd* -rf
sudo rm $(which haqqd) -rf
sudo rm $HOME/.haqqd* -rf
sudo rm $HOME/haqq -rf
sed -i '/HAQQ_/d' ~/.bash_profile
```
