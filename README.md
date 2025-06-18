# 🚀 Aztec Sequencer Node Setup (Alpha Testnet) 🟢

Running an Aztec node is very simple. You will require **$11** if you want your node to run smoothly in one go using **paid RPCs**. Alternatively, you can use **free public RPCs**, but they often come with limitations and require more setup effort.

---

## 💼 Requirements

1. A VPS to run the Aztec node (Ubuntu 20.04 or 22.04 recommended)
2. A **Paid RPC** (like from Alchemy) OR a Free Public RPC (with limitations)
3. A **Paid Beacon RPC** (or free option like `drpc.org`, `Ankr`)

---

## 🧾 Steps Overview

1. Get a VPS (Use Google Cloud, Free for new users)
2. Install dependencies 
3. Install Aztec tools
4. Setup Ethereum Wallet & RPC URLs
5. Run your node
6. Submit sync proof to get the Apprentice role on Aztec Discord

---

## ☁️ Optional: VPS Setup Using GCP

If you want to run your node on a VPS and need help setting up a Google Cloud Platform (GCP) VPS, you can use the following repository for a step-by-step guide and scripts:

- [GCP-VPS-Setup by rdkrr731](https://github.com/rdkrr731/GCP-VPS-Setup.git)

---

## 🚀 Aztec Node Setup

### 1. Install Dependencies

**Update packages:**
```bash
sudo apt-get update && sudo apt-get upgrade -y
```

**Install Packages:**
```bash
sudo apt install curl iptables build-essential git wget lz4 jq make gcc nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev  -y
```

**Install Docker:**
```bash
sudo apt update -y && sudo apt upgrade -y
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done

sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=\"$(dpkg --print-architecture)\" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  \"$(. /etc/os-release && echo \"$VERSION_CODENAME\")\" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update -y && sudo apt upgrade -y

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

**Test Docker:**
```bash
sudo docker run hello-world
```

**Enable and Restart Docker:**
```bash
sudo systemctl enable docker
sudo systemctl restart docker
```

---

### 2. Install Aztec Tools

```bash
bash -i <(curl -s https://install.aztec.network)
```

```
echo 'export PATH="$HOME/.aztec/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

**Restart your Terminal now to apply changes.**

**Check if you installed successfully:**
```bash
aztec --version
```

> **Tip:**  
> For Local System: Open your docker first and make sure you're docker is running.\
> For VPS: If you face a `docker permission denied` error, add your user to the docker group with:
> ```bash
> sudo usermod -aG docker $USER
> ```
> Then log out and back in, or restart your terminal session.

---

### 3. Update Aztec to the latest version

```bash
aztec-up latest
```

---

### 4. Obtain RPC URLs

- **L1 RPC**: You can create a Sepolia RPC URL in [Alchemy](https://alchemy.com).
- **Beacon RPC**: Use [https://rpc.drpc.org/eth/sepolia/beacon](https://rpc.drpc.org/eth/sepolia/beacon) as a free BEACON RPC. (You can also run your own prysm/lighthouse nodes or find other 3rd party solutions.)

---

### 5. Generate Ethereum Keys

- Use MetaMask or another tool to create a new Ethereum wallet.
- Save your EVM Wallet's **Private Key** and **Public Address** securely.

---

### 6. Get Sepolia ETH

- Fund your Ethereum Wallet with ETH Sepolia. You can get Sepolia ETH from [Alchemy Faucet](https://www.alchemy.com/faucets/ethereum-sepolia).

---

### 7. Find IP

```bash
curl ipv4.icanhazip.com
```
Save this IP address somewhere. You will need it later.

---

### 8. Enable Firewall & Open Ports

```bash
# Firewall
sudo ufw allow ssh
sudo ufw enable

# Sequencer
sudo ufw allow 40400
sudo ufw allow 40500
sudo ufw allow 8080
```
> **Tip:**  
> If facing Error: No command Found
> ```bash
> sudo apt update
> sudo apt install ufw -y
> ```
---

### 9. Start Sequencer Node

**Open screen or use Tmux:**
```bash
screen -S aztec
```

**Start the Node (replace the variables):**
```bash
aztec start --node --archiver --sequencer \
  --network alpha-testnet \
  --l1-rpc-urls RPC_URL  \
  --l1-consensus-host-urls BEACON_URL \
  --sequencer.validatorPrivateKey YourPrivateKey \
  --sequencer.coinbase YourEvmAddress \
  --p2p.p2pIp IP
```
- **RPC_URL & BEACON_URL:** As per step 4
- **YourPrivateKey:** Your EVM wallet private key
- **YourEvmAddress:** Your EVM wallet public address
- **IP:** Your server IP (from step 7)

---

### 10. Sync Node

Once the command is entered, your node starts running. It may take a few hours for your node to sync to the tip of the block, depending on your device's performance.

---

### 11. Get Apprentice Role on Aztec Discord

Go to the Discord channel: [Aztec Discord](https://discord.com/channels/1144692727120937080/1367196595866828982) and get the below details before using the commands.

**Open a New terminal and run (do NOT shut down your Node):**

**Step 1: Get the latest proven block number**
```bash
curl -s -X POST -H 'Content-Type: application/json' -d '{"jsonrpc":"2.0","method":"node_getL2Tips","params":[],"id":67}' http://localhost:8080 | jq -r ".result.proven.number"
```
- Save this block number for the next steps.
- Example output: `23546`
- If running on a server, replace `http://localhost:8080` with your server's IP (keep port 8080).

---

**Step 2: Generate your sync proof**
```bash
curl -s -X POST -H 'Content-Type: application/json' \
-d '{"jsonrpc":"2.0","method":"node_getArchiveSiblingPath","params":["BLOCK_NUMBER","BLOCK_NUMBER"],"id":67}' \
http://localhost:8080 | jq -r ".result"
```
- Replace both `BLOCK_NUMBER` with the number from Step 1.
  
---

🟥**Step 3: Register with Discord** 🟥 **[DEPRECATED]**

Type the following command in the Discord server:
```
/operator start
```
- Fill out the required fields:
    - **address:** Your validator address (Ethereum Address)
    - **block-number:** Block number from Step 1
    - **proof:** Sync proof (base64 string from Step 2)

Once you submit, you'll obtain your Apprentice Role!

---

🆕**Step 4: Get your Peer ID & Check Status**
- Get your Peer ID that starts with 16nlsdfl
```bash
sudo docker logs $(docker ps -q --filter ancestor=aztecprotocol/aztec:latest | head -n 1) 2>&1 | grep -i "peerId" | grep -o '"peerId":"[^"]*"' | cut -d'"' -f4 | head -n 1
```

- Go over here: [Aztec P2P Explorer](https://aztec.nethermind.io/)
- Input your Peer Id and keep track of it
---

## 📝 Notes

- Always ensure your private key is kept secure.
- Do **not** share your private key with anyone.
- For troubleshooting and support, use the [Aztec Discord](https://discord.com/invite/aztec).

---

Happy Sequencing!
