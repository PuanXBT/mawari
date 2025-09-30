# Mawari Node (Testnet) — Quick Setup

Simple walkthrough for running a **Guardian Node** on Mawari Testnet using **Ubuntu 24.04**.

---

## Requirements

* 4 CPU cores
* 8 GB RAM
* 50 GB SSD

---

## Step 0 — Wallet & Assets

1. Go to [Mawari Mint](https://testnet.mawari.net/mint) → connect wallet.
2. Claim test tokens at [Mawari Hub](https://hub.testnet.mawari.net/).
3. Mint up to 3 Guardian NFTs.

   > These NFTs will later be delegated to your node’s burner wallet.

---

## Step 1 — Install Docker

```bash
sudo apt update && sudo apt install -y ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
 | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
 https://download.docker.com/linux/ubuntu noble stable" \
 | sudo tee /etc/apt/sources.list.d/docker.list
sudo apt update && sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
docker --version
```

---

## Step 2 — Run the Node

Set environment vars (replace with your **main wallet**):

```bash
export MNTESTNET_IMAGE=us-east4-docker.pkg.dev/mawarinetwork-dev/mwr-net-d-car-uses4-public-docker-registry-e62e/mawari-node:latest
export OWNER_ADDRESS=0xYOUR_MAIN_WALLET
```

Start container:

```bash
mkdir -p ~/mawari
docker run -d --name mawari --pull always --restart unless-stopped \
 -v ~/mawari:/app/cache -e OWNERS_ALLOWLIST=$OWNER_ADDRESS $MNTESTNET_IMAGE
```

Logs:

```bash
docker logs -f mawari
```

Get burner wallet address:

```bash
docker logs mawari | grep "Using burner wallet"
```

---

## Step 3 — Fund Burner Wallet

* Send **1 MAWARI test token** from your main wallet.
* Or faucet directly for the burner address if needed.

---

## Step 4 — Delegate Guardian NFTs

1. Open [Guardian Dashboard](https://app.testnet.mawari.net/licenses).
2. Connect with main wallet.
3. Choose NFT(s) → **Delegate** → enter burner address → confirm.

---

## Step 5 — Backup Keys

```bash
cat ~/mawari/flohive-cache.json | grep privateKey
```

⚠️ Save this key securely. Losing it = losing control.

---

## Daily Commands

```bash
docker ps                      # check containers
docker logs -f mawari # follow logs
docker stop/start/restart mawari
```

Update node:

```bash
docker pull $MNTESTNET_IMAGE
docker rm -f mawari
# run docker run ... again
```

---

## Notes

* Cache folder (`~/mawari`) stores burner wallet. Don’t delete unless you want a fresh one.
* Always back up the burner private key.
* Secure your VPS (SSH keys, firewall, updates).

---

## Troubleshooting

* **Permission denied** → `sudo usermod -aG docker $USER && newgrp docker`
* **No burner wallet** → check cache folder permissions.
* **Delegation not detected** → confirm delegation direction (main → burner) and ensure burner has 1 token.

---

🚀 You’re now running a Mawari Guardian Node on testnet!
