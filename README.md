# ⚡️ Tempo RPC Node

This README installs and runs a Tempo testnet RPC node on a Linux VPS.
It uses the official Tempo installer, snapshot sync, and systemd for persistence.

The node is NON-validator and exposes Ethereum-compatible JSON-RPC APIs.

---

## Requirements -

- **CPU**:      8 cores (min)   | 16+ cores (recommended)
- **RAM**:      16 GB (min)     | 32 GB (recommended)
- **Storage**:  250 GB SSD      | 500 GB NVMe
- **Network**:  500 Mbps        | 1 Gbps

---

## Full Installation & Run Commands 👇

Run everything below as `root` on your VPS.

### System update and dependencies
```bash
apt update && apt upgrade -y
apt install -y curl git unzip lz4 ca-certificates nano ufw
```
### Install tempo using official installer
```bash
curl -L https://tempo.xyz/install | bash
source ~/.bashrc
```
### Verify tempo installation
```bash
/root/.tempo/bin/tempo --version
```
### Create screen
```bash
apt install screen -y
screen -S tempo
```

### Create data directory
```bash
mkdir -p /root/tempo
cd /root/tempo
```
### Download snapshot for fast sync
```bash
tempo download
```
- Wait until it finishes.

# Create systemd service
```bash
nano /etc/systemd/system/tempo.service
```
```bash
[Unit]
Description=Tempo RPC Node
After=network.target

[Service]
User=root
WorkingDirectory=/root/tempo
ExecStart=/root/.tempo/bin/tempo node \
  --datadir /root/tempo \
  --follow \
  --http \
  --http.addr 0.0.0.0 \
  --http.port 8545 \
  --http.api eth,net,web3,txpool,trace \
  --ws \
  --ws.addr 0.0.0.0 \
  --ws.port 8546
Restart=always
RestartSec=5
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
```

### Enable and start service
```bash
systemctl daemon-reexec
systemctl daemon-reload
systemctl enable tempo
systemctl start tempo
```

# Check logs
```bash
journalctl -u tempo -f
```

## Usefull Commands 👇

### Stop node
```bash
systemctl stop tempo
```
### Restart node
```bash
systemctl restart tempo
```
### View last logs
```bash
journalctl -u tempo -n 50
```

## For Support or Query
- 💬 **Contact**: [Md_Kaif](https://t.me/BeastMafia)
