<p style="font-size:14px" align="right">
<a href="https://t.me/PemulungAirdropID" target="_blank">Join our telegram <img src="https://user-images.githubusercontent.com/72949170/194228482-0f875615-e155-4b12-8716-8111addd6cba.jpg" width="30"/></a>
</p>


<p align="center">
  <img width="100%" height="auto" src="https://github.com/muhamad-ramadhani/mantra/assets/72949170/7cf7f452-2738-4906-8b0f-d0b60533100b">
</p>

# MANTRA TESTNET PHASE 1

|  Komponen |  Persyaratan Minimum |
| ------------ | ------------ |
| CPU  |  2vCPU (1 core)  |
| RAM | 4 GB DDR4 |
| Penyimpanan  | 200 - 500 GB HDD |
| OS | 20.04 LTS |

Dokumen Official :
> https://docs.mantrachain.io/operate-a-node/initial-setup/

Faucet :
> https://faucet.testnet.mantrachain.io/

Explorer : 
> https://testnet.itrocket.net/mantra/staking


### 1. Install 
```
wget -O mantra.sh https://raw.githubusercontent.com/muhamad-ramadhani/mantra/main/mantra.sh && chmod +x mantra.sh && ./mantra.sh
```

### 2. Check Sinkron Log
```
journalctl -fu mantrachaind -o cat
```
Tunggu sampai selesai

### 3. Check Status Node
```
mantrachaind status 2>&1 | jq .SyncInfo
```
Jika status sudah "FALSE" maka bisa melanjutkan ke step berikutnya

### 4. Load Sistem
```
source $HOME/.bash_profile
```

### 5. Create Wallet
```
mantrachaind keys add $WALLET
```
- Simpan Address & Mnemonic kalian
- Enter Keyring Phase Buat Password bebas

### 6. Save wallet information
```
MANTRA_WALLET_ADDRESS=$(mantrachaind keys show $WALLET -a)
MANTRA_VALOPER_ADDRESS=$(mantrachaind keys show $WALLET --bech val -a)
echo 'export MANTRA_WALLET_ADDRESS='${MANTRA_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export MANTRA_VALOPER_ADDRESS='${MANTRA_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### 7. Claim Faucet
Ambil Faucet disini :
https://faucet.testnet.mantrachain.io/

### 8. Check Balance
```
mantrachaind q bank balances $MANTRA_WALLET_ADDRESS
```
Cek faucetnya, kalau dah masuk bisa ke next step

### 9. Create Validator
```
mantrachaind tx staking create-validator \
  --amount 1000000uaum \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.10" \
  --min-self-delegation "1" \
  --pubkey  $(mantrachaind tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $MANTRA_CHAIN_ID \
  --gas auto --gas-adjustment 1.5 --fees 50uaum \
 -y
```

### 10. Update detail validator
```
mantrachaind tx staking edit-validator \
--new-moniker=$NODENAME \
--identity=<your_keybase_id> \
--website="<your_website>" \
--details="<your_validator_description>" \
--chain-id=$MANTRA_CHAIN_ID \
--from=$WALLET
```

- <your_keybase_id> Ganti Dengan ID Keybase Kalian
- <your_website> Ganti Dengan Link Twitter atau Website Kalian
- <your_validator_description> Ganti Dengan Deskripsi Node Kalian

### 11. Delegate
```
mantrachaind tx staking delegate $VALOPER_ADDRESS 10000000uaum --from $WALLET --chain-id $MANTRA_CHAIN_ID --fees 5000uaum
```

### DONE



## Another Command

### Delete Node
```
sudo systemctl stop mantrachaind
sudo systemctl disable mantrachaind
sudo rm /etc/systemd/system/mantrachaind.service
sudo systemctl daemon-reload
rm -f $(which mantrachaind)
rm -rf .mantrachain
rm -rf mantrachaind
```

### Redelegate
```
mantrachaind tx staking redelegate $VALOPER_ADDRESS <dst-validator-operator-addr> 100000000uaum --from=$WALLET --chain-id=$MANTRA_CHAIN_ID
```

### Withdraw
```
mantrachaind tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$MANTRA_CHAIN_ID
```

### Unjail
```
mantrachaind tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$MANTRA_CHAIN_ID \
  --gas=auto \
  --gas-adjustment=1.4
```
