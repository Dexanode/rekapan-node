## NODE NODE AN : `node`
- Discord link is [here](https://discord.gg/mSecguCyht)
- Twitter [here](https://twitter.com/rehan_ssf)
- Telegram @paperhang
- 
# Instructions

# Another auto install
```
wget -q -O another-1.sh https://raw.githubusercontent.com/AKnode/rekapan-node/main/Another-1/another-1.sh && chmod +x another-1.sh && sudo /bin/bash another-1.sh
```

### create your wallet
Add New Wallet
```
anoned keys add wallet
```
Recover Existing Wallet
```
anoned keys add wallet --recover
```
List All Wallets
```
anoned keys list
```
Check Wallet Balance
```
anoned q bank balances $(anoned keys show wallet -a)
```