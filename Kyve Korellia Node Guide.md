![alt text](https://i.hizliresim.com/9fa8ruc.png)

**Minimum requirements**

- **2vCPU**
- **4GB RAM**
- **80GB DİSK**
- **Ubuntu 20.04+**

**Updates and Downloads**
```
sudo apt update && sudo apt upgrade -y
```
```
apt install wget tar curl jq -y
```
```
wget https://github.com/KYVENetwork/chain/releases/download/v0.3.0/chain_linux_amd64.tar.gz
```
```
tar -xvzf chain_linux_amd64.tar.gz
```
```
chmod +x chaind
```
```
mv chaind /usr/local/bin/kyved
```

**After that, we will set the moniker and the wallet name.**

- **Just YOUR_MONIKER_NAME and YOUR_WALLET_NAME change it**

```
KYVE_CHAIN="korellia"

KYVE_MONIKER="YOUR_MONIKER_NAME"

KYVE_WALLET="YOUR_WALLET_NAME"
```

**Add variables to .bash_profile**
```
echo 'export KYVE_CHAIN='${KYVE_CHAIN} >> $HOME/.bash_profile
```
```
echo 'export KYVE_MONIKER='${KYVE_MONIKER} >> $HOME/.bash_profile
```
```
echo 'export KYVE_WALLET='${KYVE_WALLET} >> $HOME/.bash_profile
```
```
source $HOME/.bash_profile
```

**Enter the code to start**

```
kyved init $KYVE_MONIKER --chain-id $KYVE_CHAIN
```

- **You should get ibr output as in the picture**

![alt text](https://i.hizliresim.com/akqelp4.png)


**Genesis file download and migration**

```
wget https://github.com/KYVENetwork/chain/releases/download/v0.0.1/genesis.json
```
```
mv genesis.json ~/.kyve/config/genesis.json
```

**Seed Loading**

```
seeds="e56574f922ff41c68b80700266dfc9e01ecae383@18.156.198.41:26656"
```
```
sed -i.bak -e "s/^seeds *=.*/seeds = \"$seeds\"/" $HOME/.kyve/config/config.toml
```

**Creating a wallet**

- **Don't forget your mnemonics and wallet password!**
```
kyved keys add $KYVE_WALLET
```

- **After you set your wallet password, you will receive a printout like this. Save your mnemonics in this output**

![alt text](https://i.hizliresim.com/hbgbyf1.png)

- **If you already have a wallet, enter the code below and then enter the wallet mnemonics**

```
kyved keys add $KYVE_WALLET --recover
```

- **Adding wallet address to variable (it will ask for wallet password)**

```
KYVE_ADDR=$(kyved keys show $KYVE_WALLET -a)
```

- **Add variable to .bash_profile**

```
echo 'export KYVE_ADDR='${KYVE_ADDR} >> $HOME/.bash_profile
```
```
source $HOME/.bash_profile
```

**Creating a service file**

```
tee $HOME/kyved.service > /dev/null <<EOF
[Unit]
Description=kyve
After=network.target

[Service]
Type=simple
User=$USER
ExecStart=$(which kyved) start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

- **Move the service file to the working folder**

```
sudo mv $HOME/kyved.service /etc/systemd/system/
```

- **Run the File**

```
sudo systemctl daemon-reload
```
```
sudo systemctl enable kyved
```
```
sudo systemctl restart kyved
```  

**Snapshot Operations (this may take some time)**

```
sudo systemctl stop kyved
```
```
cd
```
```
rm -r .kyve/data/*
```
```
cd .kyve/data
```
```
wget https://snapshots.bitszn.com/snapshots/kyve/kyve.tar
```
```
tar -xf kyve.tar
```
```
sudo systemctl start kyved
```

 **Addrbook Transactions**
 
```
sudo systemctl stop kyved
```
```
cd
```
```
cd .kyve/config
```
```
rm addrbook.json
```
```
wget https://snapshots.bitszn.com/snapshots/kyve/addrbook.json
```
```
sudo systemctl start kyved
```   

 **Checking the logs**
 
``` 
journalctl -u kyved -f -o cat
```

- **You should get an output like this**

![alt text](https://i.hizliresim.com/f813lbg.png)


**Sync status (enter this code on another page in terminal)**

```
curl -s localhost:26657/status
```
- **You will get this output**

![alt text](https://i.hizliresim.com/c3rvi4s.png)

- **Catching-up: Wait until False (max 15 min)**


- **Request tokens on the KYVE Discord Faucet channel. Will send 20 KYVE (example picture below)**


![alt text](https://i.hizliresim.com/qibls3h.png)


- **Balance Check**

```
kyved query bank balances $KYVE_ADDR
```

**Creating a validator**

```
kyved tx staking create-validator --yes \ 
--amount 20000000000tkyve \ 
--moniker $KYVE_MONIKER \ 
--commission-rate "0.10" \ 
--commission-max-rate "0.20" \ 
--commission-max-change-rate "0.01" \ 
--min-self-delegation "1" \ 
--pubkey "$(kyved tendermint show-validator)" \ 
--from $KYVE_WALLET \ 
--chain-id korellia
```

**Enter this code if Validator is Jailed**

```
kyved tx slashing unjail --chain-id korellia --from $KYVE_WALLET
```

**You can check your validator from this site.**

https://kyve.explorers.guru/validators

**Thank You..**








