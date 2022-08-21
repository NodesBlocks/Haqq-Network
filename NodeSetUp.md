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
wget -O HAQQ.sh https://raw.githubusercontent.com/NodesBlocks/main/Haqq-Network/HAQQ && chmod +x HAQQ.sh && ./HAQQ.sh
```
## Post-Installation Steps

You should make sure your validator syncs blocks. You can use the following command to check the sync status.
```bash
haqqd status 2>&1 | jq .SyncInfo
```
## Creating a Wallet
You can use the following command to create a new wallet. Do not forget to save the reminder (mnemonic).
```bash
haqqd keys add $HAQQ_WALLET
```
(OPTIONAL) To recover your wallet using mnemonic:
```bash
haqqd keys add $HAQQ_WALLET --recover
```
To get the current wallet list
```bash
haqqd keys list
```
## Save Wallet Information

Add Wallet Address:
```bash
HAQQ_WALLET_ADDRESS=$(haqqd keys show $HAQQ_WALLET -a)
HAQQ_VALOPER_ADDRESS=$(haqqd keys show $HAQQ_WALLET --bech val -a)
echo 'export HAQQ_WALLET_ADDRESS='${HAQQ_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export HAQQ_VALOPER_ADDRESS='${HAQQ_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```
## Create validator

Before creating a validator please make sure you have at least 1 ISLM (1 ISLM equals 1000000 aISLM) and your node is in sync.

To check your wallet balance
```bash
haqqd query bank balances $HAQQ_WALLET_ADDRESS
```
If you can't see your balance in your wallet, chances are your node is still syncing. Please wait for the sync to finish and then continue

Creating a Validator:
```bash
haqqd tx staking create-validator \
  --amount 1000000aISLM \
  --from $HAQQ_WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(haqqd tendermint show-validator) \
  --moniker $HAQQ_NODENAME \
  --chain-id $HAQQ_ID \
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
haqqd keys add $HAQQ_WALLET --recover
```
Wallet Delete:
```bash
haqqd keys delete $HAQQ_WALLET
```
Show Wallet Balance:
```bash
haqqd query bank balances $HAQQ_WALLET_ADDRESS
```
Transfer tokens between wallets:
```bash
haqqd tx bank send $HAQQ_WALLET_ADDRESS <TO_WALLET_ADDRESS> 10000000aISLM
```
***Voting***
```bash
haqqd tx gov vote 1 yes --from $HAQQ_WALLET --chain-id=$HAQQ_ID
```
***Stake, Delegation and Rewards***

Delegate Process:
```bash
haqqd tx staking delegate $HAQQ_VALOPER_ADDRESS 10000000aISLM --from=$HAQQ_WALLET --chain-id=$HAQQ_ID --gas=auto --fees 250aISLM
```
Redelegate from validator to another validator:
```bash
haqqd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 10000000aISLM --from=$HAQQ_WALLET --chain-id=$HAQQ_ID --gas=auto --fees 250aISLM
```
Withdraw all rewards:
```bash
haqqd tx distribution withdraw-all-rewards --from=$HAQQ_WALLET --chain-id=$HAQQ_ID --gas=auto --fees 250aISLM
```
Withdraw rewards with commission:
```bash
haqqd tx distribution withdraw-rewards $HAQQ_VALOPER_ADDRESS --from=$HAQQ_WALLET --commission --chain-id=$HAQQ_ID
```
***Validator Management***

Change Validator Name:
```bash
haqqd tx staking edit-validator \
--moniker=NEWNODENAME \
--chain-id=$HAQQ_ID \
--from=$HAQQ_WALLET
```
Get Out Of Jail(Unjail):
```bash
haqqd tx slashing unjail \
  --broadcast-mode=block \
  --from=$HAQQ_WALLET \
  --chain-id=$HAQQ_ID \
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





















