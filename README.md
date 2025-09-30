# Mawari Guardian Node (Testnet) — Step by Step Setup

Panduan ini membantu menjalankan **Guardian Node** Mawari Testnet di **Ubuntu 24.04**.
Semua command dipisah baris demi baris supaya mudah untuk pemula.

---

## Persyaratan Sistem

* **CPU**: 4 vCPU
* **RAM**: 8 GB
* **Disk**: 50 GB SSD
* **OS**: Ubuntu 24.04

---

## 0 — Siapkan Wallet & NFT

1. Kunjungi [Mawari Mint](https://testnet.mawari.net/mint) → connect wallet.
2. Claim faucet token (maks 2) di [Mawari Hub](https://hub.testnet.mawari.net/).
3. Mint sampai 3 Guardian NFT di **MAIN wallet**.

---

## 1 — Install Docker

Jalankan perintah berikut satu per satu:

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

Set variabel (ganti alamat dengan wallet utama yang pegang NFT):

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

**Penting:** simpan private key dengan aman (jangan dishare, jangan upload ke repo publik).

---

## 6 — Perintah Harian

Cek container jalan:

```bash
docker ps
```

Lihat log:

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

## 7 — Update Node

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

  * Pastikan delegasi dari MAIN → burner
  * Burner harus pegang 1 token test

---
