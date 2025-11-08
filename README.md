# <h2 align=center> Aztec Nodes </h2>

- Buy VPS : [Skuy Vibes](https://t.me/skuyvibes/156) `<---`
- Trakteer buy Coffee : https://trakteer.id/brrrskuy/tip `<---`
-------------------------------------

## Step 1 (Install Tools)
### 1. Install Foundry
```
sudo apt update
sudo apt install -y build-essential pkg-config libssl-dev curl git
curl -L https://foundry.paradigm.xyz | bash
```
### 2. Add to path Foundry
```
echo 'export PATH="$HOME/.foundry/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```
### 3. Update Foundry
```
foundryup
```
### 4. Verify your Install
```
cast --version
forge --version
anvil --version
```
### 5. Install Aztec CLI 
```
bash -i <(curl -s https://install.aztec.network)
```
### 6. Add to path Aztec
```
echo 'export PATH="$HOME/.aztec/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```
### 7. Install Aztec new version
```
aztec-up 2.1.2
```
### 8. Verify your install
```
aztec --version
```
-------------------------------------
## Step 2 (Register Validator)
### 1. Use Old Keystore with Aztec CLI
```
aztec validator-keys new \
  --fee-recipient 0x0000000000000000000000000000000000000000000000000000000000000000 \
  --mnemonic "CHANGE_TO_YOUR_MNEMONIC_YOUR_WALLET_HERE"
```
make sure you have 0.5-1 ETH Sepolia
### 2. Generate your BLS Private Keys
```
aztec generate-bls-keypair --mnemonic "CHANGE_TO_YOUR_MNEMONIC_YOUR_WALLET_HERE" --json
```
don't forget to save your Keystore + BLS 
### 3. Approve Aztec rollup spend 200k STK
```
cast send 0x139d2a7a0881e16332d7D1F8DB383A4507E1Ea7A "approve(address,uint256)" 0xebd99ff0ff6677205509ae73f93d0ca52ac85d67 200000ether --private-key "CHANGE_PRIVATE_KEY_OLD_SEQUENCER" --rpc-url $ETH_RPC
```
### 4. Register your Validator to Aztec Network
```
aztec \
  add-l1-validator \
  --l1-rpc-urls YOUR_ETH_RPC \
  --network testnet \
  --private-key PRIVATE_KEY_OF_OLD_SEQUENCER \
  --attester ETH_ATTESTER_ADDRESS_OLD \
  --withdrawer YOUR_ETH_ADDRESS_OLD \
  --bls-secret-key BLS_ATTESTER_PRIV_KEY \
  --rollup 0xebd99ff0ff6677205509ae73f93d0ca52ac85d67
```
don't forget to change the requirements

check your wallet if success to Join > [Dashtec Queue](dashtec.xyz/queue)

-------------------------------------
## Step 3 (Pre-required files)
### 1. Create new directory
```
mkdir -p /root/aztec/keys
mkdir -p /root/aztec/data
cd /root/aztec
```
Directory structure:

`/root/aztec/keys/` - Contains keystore files

`/root/aztec/data/` - Contains node data

`/root/aztec/` - Contains docker-compose.yml and .env files
### 2. Update your keystore
1
```
ETH_KEY=$(jq -r '.validators[0].attester.eth' ~/.aztec/keystore/key1.json)
BLS_KEY=$(jq -r '.validators[0].attester.bls' ~/.aztec/keystore/key1.json)
FEE_RECIPIENT=$(jq -r '.validators[0].feeRecipient // "0x0000000000000000000000000000000000000000000000000000000000000000"' ~/.aztec/keystore/key1.json)
```
2
```
{
  "schemaVersion": 1,
  "validators": [
    {
      "attester": {
        "eth": "$ETH_KEY",
        "bls": "$BLS_KEY"
      },
      "coinbase": "CHANGE_YOUR_ADDRESS",
      "feeRecipient": "$FEE_RECIPIENT"
    }
  ]
}
EOF
```
don't forget to change `coinbase` address
#### allow the proses
```
chmod 600 /root/aztec/keys/keystore.json
```
#### check file
```
cat /root/aztec/keys/keystore.json | jq .
```
#### Example format
```
{
  "schemaVersion": 1,
  "validators": [
    {
      "attester": {
        "eth": "0x...",
        "bls": "0x..."
      },
      "coinbase": "0x...",
      "feeRecipient": "0x..."
    }
  ]
}
```
### 3. Delete your old datas
```
rm -rf .aztec
```
-------------------------------------
## Step 4 (Running Nodes docker)
### 1. Create .env file
```
cd /root/aztec
nano .env
```
### 2. Paste to your .env file
```
ETHEREUM_RPC_URL=CHANGE_YOUR_OWN_RPC
CONSENSUS_BEACON_URL=CHANGE_YOUR_OWN_RPC
GOVERNANCE_PROPOSER_PAYLOAD_ADDRESS=0xDCd9DdeAbEF70108cE02576df1eB333c4244C666
P2P_IP=CHANGE_YOUR_PUBLIC_IP_VPS
P2P_PORT=40400
AZTEC_PORT=8080
LOG_LEVEL=info
```
if done back to screen `CTRL+S+X` 
### 3. Create docker-compose.yaml
```
services:
  aztec-node:
    container_name: aztec-sequencer
    image: aztecprotocol/aztec:2.1.2
    restart: unless-stopped
    network_mode: host
    environment:
      GOVERNANCE_PROPOSER_PAYLOAD_ADDRESS: ${GOVERNANCE_PROPOSER_PAYLOAD_ADDRESS}
      ETHEREUM_HOSTS: ${ETHEREUM_RPC_URL}
      L1_CONSENSUS_HOST_URLS: ${CONSENSUS_BEACON_URL}
      DATA_DIRECTORY: /var/lib/data
      KEY_STORE_DIRECTORY: /var/lib/keystore
      P2P_IP: ${P2P_IP}
      P2P_PORT: ${P2P_PORT:-40400}
      AZTEC_PORT: ${AZTEC_PORT:-8080}
      LOG_LEVEL: ${LOG_LEVEL:-info}
    entrypoint: >
      sh -c 'node --no-warnings /usr/src/yarn-project/aztec/dest/bin/index.js start --network testnet --node --archiver --sequencer'
    ports:
      - 40400:40400/tcp
      - 40400:40400/udp
      - 8080:8080
    volumes:
      - /root/aztec/data:/var/lib/data
      - /root/aztec/keys:/var/lib/keystore
```
### 4. Start your nodes
```
docker compose up -d
```
### 5. check your logs
```
docker compose logs -fn 100
```
----------------------------------------------------------
### Join Telegram Channel🚀
For Updates, Questions and other Guides Join the Community :

[Skuy Vibes](https://t.me/skuyvibes) or [Airdrop Family IDN](https://t.me/AirdropFamilyIDN)
