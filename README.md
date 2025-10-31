## Hardware Recruitmens
- RAM: 16GB
- CPU: 8
  

## Step 0: Install Dependencies

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl wget git jq build-essential pkg-config libssl-dev openssl docker.io docker-compose
```

Verify Docker

```bash
sudo systemctl enable docker
sudo systemctl start docker
docker --version
```

Install Solana Cli

```bash
wget https://raw.githubusercontent.com/solana-labs/solana/v1.18.18/install/solana-install-init.sh -O solana-install-init.sh
sh solana-install-init.sh stable
```

Check Version

```bash
solana --version
```

If output like this

```
solana-cli 1.18.26 (src:d9f20e95; feat:3241752014, client:SolanaLabs)
```

Solana cli allready installed

Set network to devnet
```bash
solana config set — url https://api.devnet.solana.com
```
## Step 1 set up workspace

```bash
mkdir arcium-node-setup
cd arcium-node-setup
```

Get Public IP

```bash
curl -4 https://ipecho.net/plain; echo
```

## Step 2 Install Arcium tools

```bash
TARGET=x86_64_linux
curl "https://bin.arcium.com/download/arcup_${TARGET}_0.3.0" -o ~/.cargo/bin/arcup
chmod +x ~/.cargo/bin/arcup

arcup install

arcium --version
```

verify_Installation

```bash
which arcium
```

should_print

```
/root/.cargo/bin/arcium
```

##Step 3: Generate Required Keypairs

```bash
solana-keygen new --outfile node-keypair.json --no-bip39-passphrase
solana-keygen new --outfile callback-kp.json --no-bip39-passphrase
openssl genpkey -algorithm Ed25519 -out identity.pem
```

## Step 4: Fund Your Accounts

```bash
solana address --keypair node-keypair.json
solana address --keypair callback-kp.json
solana airdrop 2 <node-pubkey> -u devnet
solana airdrop 2 <callback-pubkey> -u devnet
```

Solana Devnet Faucet: https://faucet.solana.com

## Step 5: Initialize Node Accounts

```bash
arcium init-arx-accs \
  --keypair-path node-keypair.json \
  --callback-keypair-path callback-kp.json \
  --peer-keypair-path identity.pem \
  --node-offset <your-node-offset> \
  --ip-address <YOUR_PUBLIC_IP> \
  --rpc-url https://api.devnet.solana.com
```
  
For node offset chose 8–10 digitsnumber eg : 53456743

## Step 6 Create file node-config.toml:

```bash
nano node-config.toml
```

Copy this and change <your-node-offset> with your own offset

```
[node]
offset = <your-node-offset>
hardware_claim = 0
starting_epoch = 0
ending_epoch = 9223372036854775807
[network]
address = "0.0.0.0"
[solana]
endpoint_rpc = "https://api.devnet.solana.com"
endpoint_wss = "wss://api.devnet.solana.com"
cluster = "Devnet"
commitment.commitment = "confirmed"
```
## Step 7: Cluster Operations

There's 2 option you can create Cluster or Join my cluster

#### Option A Join My Cluster:

```Bash
arcium join-cluster true \
  --keypair-path node-keypair.json \
  --node-offset <your-offset> \
  --cluster-offset 69069069 \
  --rpc-url https://api.devnet.solana.com
```
  Change your <your-offset> with your own

#### Option B create Cluster:  

```bash
arcium init-cluster \
  --keypair-path node-keypair.json \
  --offset <cluster-offset> \
  --max-nodes 10 \
  --rpc-url https://api.devnet.solana.com
```
  Create your <cluster-offset> with 8 - 10 digit number different with your node offset

## Step 8: Deploy Your Node with Docker

```bash
nano docker-compose.yml
```

copy this to docker-compose.yml
```
version: '3.8'

services:
  arx-node:
    image: arcium/arx-node
    container_name: arx-node
    environment:
      - NODE_IDENTITY_FILE=/usr/arx-node/node-keys/node_identity.pem
      - NODE_KEYPAIR_FILE=/usr/arx-node/node-keys/node_keypair.json
      - OPERATOR_KEYPAIR_FILE=/usr/arx-node/node-keys/operator_keypair.json
      - CALLBACK_AUTHORITY_KEYPAIR_FILE=/usr/arx-node/node-keys/callback_authority_keypair.json
      - NODE_CONFIG_PATH=/usr/arx-node/arx/node_config.toml
    volumes:
      - ./node-config.toml:/usr/arx-node/arx/node_config.toml
      - ./node-keypair.json:/usr/arx-node/node-keys/node_keypair.json:ro
      - ./node-keypair.json:/usr/arx-node/node-keys/operator_keypair.json:ro
      - ./callback-kp.json:/usr/arx-node/node-keys/callback_authority_keypair.json:ro
      - ./identity.pem:/usr/arx-node/node-keys/node_identity.pem:ro
      - ./arx-node-logs:/usr/arx-node/logs
    ports:
      - "8080:8080"
    restart: unless-stopped
```

Start Node
```bash
docker compose up -d
```

## Step 9: Verify Node Operation

```bash
arcium arx-info <your-node-offset> --rpc-url https://api.devnet.solana.com
arcium arx-active <your-node-offset> --rpc-url https://api.devnet.solana.com
```
Check logs
```bash
tail -f ~/arcium-node-setup/arx-node-logs/arx_log_16_10_2025_16:10_87347420.log
```

How to find your log name?
```bash
cd ~/arcium-node-setup/arx-node-logs/
ls -lh
```
you will see like

arx_log_16_10_2025_16:10_87347420.log

this is mine, change with your own

That's all




