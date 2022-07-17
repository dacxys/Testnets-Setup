# <h1 align="center">StaFiHub Public Testnet 3</h1>

# System requirements: NOTE: I think you can install it on the server for free by doing the purring and index settings below
```
8GB RAM
200 GB SSD
4 vCPU
```

# Staffihub tesnet - 2 participants should delete their files first:
```
sudo systemctl stop stafihubd && \
sudo systemctl disable stafihubd && \
rm /etc/systemd/system/stafihubd.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf .stafihub stafihub && \
rm -rf $(which stafihubd)
```

# Start:
```
sudo su
```

# Go to root cd:
```
cd /root
```

# System update:
```
sudo apt update && sudo apt upgrade -y
```

# Install Library:
```
sudo apt install make clang pkg-config libssl-dev build-essential git jq ncdu bsdmainutils -y < "/dev/null"
```

# Go setup:
```
cd $HOME
wget -O go1.18.2.linux-amd64.tar.gz https://go.dev/dl/go1.18.2.linux-amd64.tar.gz
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.18.2.linux-amd64.tar.gz && rm go1.18.2.linux-amd64.tar.gz
echo 'export GOROOT=/usr/local/go' >> $HOME/.bashrc
echo 'export GOPATH=$HOME/go' >> $HOME/.bashrc
echo 'export GO111MODULE=on' >> $HOME/.bashrc
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bashrc && . $HOME/.bashrc
go version
```

# Clone Stafi'nin github files
```
git clone --branch public-testnet-v3 https://github.com/stafihub/stafihub
```

# Stafihub install:
```
cd $HOME/stafihub && make install
```

# initialize, nodename should be changed with  your own nodename
```
stafihubd init NodeName --chain-id stafihub-public-testnet-3
```

# Genesis file download:
```
wget -O $HOME/.stafihub/config/genesis.json "https://raw.githubusercontent.com/stafihub/network/main/testnets/stafihub-public-testnet-3/genesis.json"
```

# Node configurations:
```
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.01ufis\"/" $HOME/.stafihub/config/app.toml
sed -i '/\[grpc\]/{:a;n;/enabled/s/false/true/;Ta};/\[api\]/{:a;n;/enable/s/false/true/;Ta;}' $HOME/.stafihub/config/app.toml
PEERS="6e9d988bf9812b02c46dcec474591bd10f81916f@45.94.58.160:26656,06c57407aea673fca396b01581a2d92957d48c4a@149.102.143.60:26656,5a6d8e1904c88c9f72d35df63b15d14504aaf030@164.92.159.170:26656,5e88d0d6866cd2f386e885de6eb0a1e3bd4f45c5@38.242.237.130:26656,1eaff7a3defa35de2b29f28d4729317d783f606c@149.102.139.101:26656,724430a2cf42b94f5da6b24d4741c7418fefa24e@194.60.201.153:26656,aae1ac9ef12897d7dc8755240cbdc41ee1171a55@38.242.215.200:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.stafihub/config/config.toml
```

# Pruning (Decrease disk usage - increase cpu ve ram usage) (Optional)
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="50"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.stafihub/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.stafihub/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.stafihub/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.stafihub/config/app.toml
```

# Close İndexer (Decrease Disk usage) (Optional)
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.stafihub/config/config.toml
```

# Create service file
```
echo "[Unit]
Description=StaFiHub Node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which stafihubd) start
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target" > $HOME/stafihubd.service
sudo mv $HOME/stafihubd.service /etc/systemd/system
sudo tee <<EOF >/dev/null /etc/systemd/journald.conf
Storage=persistent
EOF
sudo systemctl restart systemd-journald
sudo systemctl daemon-reload
sudo systemctl enable stafihubd
sudo systemctl restart stafihubd
```

# Create your wallet. walletName should be changed with your own name:
```
stafihubd keys add walletName
```

# recover wallet if you already have:
```
stafihubd keys add walletName --recover
```

# Faucet to get fund: https://discord.gg/tz6USZWX

# Check whether you are synced with below code. You should get false output:
```
stafihubd status 2>&1 | jq .SyncInfo
```

# Create Validator: 
```
stafihubd tx staking create-validator \
--moniker="NodeName" \
--amount=48885000ufis \
--gas auto \
--fees=5000ufis \
--pubkey=$(stafihubd tendermint show-validator) \
--chain-id=stafihub-public-testnet-3 \
--commission-max-change-rate=0.01 \
--commission-max-rate=0.20 \
--commission-rate=0.10 \
--min-self-delegation=1 \
--from=WalletName \
--yes
```

# You should have success ouput with txhash you received when you paste it in explorer.

# Explorer link: https://testnet-explorer.stafihub.io/stafi-hub-testnet



















