# Mawari Guardian Node (Testnet) — Step by Step Setup
---

## Persyaratan Sistem

* **CPU**: 4 vCPU
* **RAM**: 8 GB
* **Disk**: 50 GB SSD
* **OS**: Ubuntu 24.04

---

## 0 — Siapkan Wallet & NFT

1. Claim faucet [Mawari Hub](https://hub.testnet.mawari.net/)
2. Mint NFT Testnet [Mawari Mint](https://testnet.mawari.net/mint)
## Max mint 3 nft

---

## 1 — Install Docker

Jalankan perintah berikut :

```bash
sudo apt update -y
```

```bash
sudo apt install -y ca-certificates curl gnupg
```

```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

```bash
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu noble stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

```bash
sudo apt update -y
```

```bash
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Cek versi:

```bash
docker --version
```

---

## 2 — Jalankan Node (container: `mawari`)

Set variabel (ganti alamat dengan wallet utama yang dipake mint NFT):

```bash
export MNTESTNET_IMAGE=us-east4-docker.pkg.dev/mawarinetwork-dev/mwr-net-d-car-uses4-public-docker-registry-e62e/mawari-node:latest
```

```bash
export OWNER_ADDRESS=0xISIWALLETMU(yg mint NFT)
```

Buat folder cache:

```bash
mkdir -p ~/mawari
```

Jalankan container:

```bash
docker run -d --name mawari --pull always --restart unless-stopped \
-v ~/mawari:/app/cache -e OWNERS_ALLOWLIST=$OWNER_ADDRESS $MNTESTNET_IMAGE
```

Cek log realtime:

```bash
docker logs -f mawari
```

Ambil alamat burner wallet:

```bash
docker logs mawari | grep -i "Using burner wallet"
```

---

## 3 — Fund Burner Wallet

* Kirim **1 MAWARI test token** dari MAIN wallet → burner wallet.
* Kalau cuma punya 1 token, faucet lagi langsung ke burner wallet.

---

## 4 — Delegasikan Guardian NFT

1. Buka [Guardian Dashboard](https://app.testnet.mawari.net/licenses).
2. Connect wallet utama.
3. Pilih NFT → klik **Delegate** → masukkan burner address → konfirmasi.

---

## 5 — Backup Private Key Burner

File tersimpan di `~/mawari/flohive-cache.json`.

Tampilkan private key:

```bash
grep -i "privateKey" ~/mawari/flohive-cache.json
```

**Penting:** simpan private key dengan aman (AMANKAN PK).

---

## 6 — Command Lainya

Cek container jalan/Kaga:

```bash
docker ps
```

Pantau logs:

```bash
docker logs -f mawari
```

Stop / start / restart:

```bash
docker stop mawari
docker start mawari
docker restart mawari
```

---

## 7 — Update Node (Jika Nanti Ada Update :

Tarik image terbaru:

```bash
docker pull $MNTESTNET_IMAGE
```

Hapus container lama:

```bash
docker rm -f mawari
```

Jalankan ulang:

```bash
docker run -d --name mawari --pull always --restart unless-stopped \
-v ~/mawari:/app/cache -e OWNERS_ALLOWLIST=$OWNER_ADDRESS $MNTESTNET_IMAGE
```

---

## Troubleshooting

* **Permission denied saat docker run**

  ```bash
  sudo usermod -aG docker $USER
  newgrp docker
  ```

* **Burner address tidak muncul**

  * Pastikan folder `~/mawari` ada & writable
  * Restart container lalu cek log lagi

* **Delegasi gagal**

  * Pastikan delegasi dari MAIN wallet ke → burner Wallet Address 
  * Pastikan Burn Wallet Ada Faucetnya

---
