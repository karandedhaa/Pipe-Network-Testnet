## 💻 System Requirements
| Resource     | Requirement   |
|--------------|---------------|
| CPU          | 4 Cores       |
| RAM          | 16 GB         |
| Storage      | 25 GB SSD     |
| Internet     | 1 Gbps        |
> [!NOTE]
> Only those who have been invited via email are able to join this testnet.
## 📥 Installation
### 1. Update Packages & Install Dependencies
```
sudo apt update && sudo apt install libssl-dev ca-certificates -y
```
- Make sure port 443 and 80 are open:
```
sudo ufw allow 443/tcp
sudo ufw allow 80/tcp
```
### 2.Configure Network Settings for Optimal Performance:
```
sudo bash -c 'cat > /etc/sysctl.d/99-popcache.conf << EOL
net.ipv4.ip_local_port_range = 1024 65535
net.core.somaxconn = 65535
net.ipv4.tcp_low_latency = 1
net.ipv4.tcp_fastopen = 3
net.ipv4.tcp_slow_start_after_idle = 0
net.ipv4.tcp_window_scaling = 1
net.ipv4.tcp_wmem = 4096 65536 16777216
net.ipv4.tcp_rmem = 4096 87380 16777216
net.core.wmem_max = 16777216
net.core.rmem_max = 16777216
EOL'

sudo sysctl -p /etc/sysctl.d/99-popcache.conf
```
```
sudo sysctl -p /etc/sysctl.d/99-popcache.conf
```
### 3. Increase File Limits:
```
sudo bash -c 'cat > /etc/security/limits.d/popcache.conf << EOL
*    hard nofile 65535
*    soft nofile 65535
EOL'
```
### 4. Create Directory:
```
sudo mkdir -p /opt/popcache/logs
cd /opt/popcache
```
### 5. Download and Extract the Binary Files:
```
sudo wget -P /opt/popcache https://download.pipe.network/static/pop-v0.3.0-linux-x64.tar.gz
sudo tar -xzf pop-v0.3.0-linux-*.tar.gz
sudo chmod 755 /opt/popcache/pop
```
### 6. Open & Edit Config File:
```
sudo nano config.json
```
- Paste the following code in the file
```
{
  "pop_name": "your-pop-name",
  "pop_location": "Your Location, Country",
  "invite_code": "your-invite-code",
  "server": {
    "host": "0.0.0.0",
    "port": 443,
    "http_port": 80,
    "workers": 40
  },
  "cache_config": {
    "memory_cache_size_mb": 4096,
    "disk_cache_path": "./cache",
    "disk_cache_size_gb": 100,
    "default_ttl_seconds": 86400,
    "respect_origin_headers": true,
    "max_cacheable_size_mb": 1024
  },
  "api_endpoints": {
    "base_url": "https://dataplane.pipenetwork.com"
  },
  "identity_config": {
    "node_name": "your-node-name",
    "name": "Your Name",
    "email": "your.email@example.com",
    "website": "https://your-website.com",
    "twitter": "your_twitter_handle",
    "discord": "your_discord_username",
    "telegram": "your_telegram_handle",
    "solana_pubkey": "YOUR_SOLANA_WALLET_ADDRESS_FOR_REWARDS"
  }
}
```
- Once all necessary adjustments have been made, hit `ctrl+x` `y` `enter`
#### Configuration tips:
1. **Memory settings**:
   - Set `memory_cache_size_mb` based on your available RAM (50-70% of total RAM is a good starting point)
   - Example: For a 16GB RAM server, set to 8192-10240
    
2. **Disk settings**:
   - Set `disk_cache_size_gb` based on your available disk space (leave at least 20% free)
   - Example: For a 500GB disk, set to 350-400

3. **Worker settings**:
   - For best performance, set `workers` to 0 which auto-detects the number of CPU cores
   - For specific control, set to the number of CPU cores minus 1

4. **Network binding**:
   - Set `server.host` to `0.0.0.0` to bind to all network interfaces
   - For security in development, use `127.0.0.1` to only accept local connections

5. **Identity configuration**:
   - All fields under `identity_config` are important for proper attribution
   - The `solana_pubkey` field is required for receiving rewards
> [!NOTE]
> Remember to replace your real data for the remaining data.
### 7. Create a Systemd Service:
```
sudo nano /etc/systemd/system/popcache.service
```
- Paste the following content in the file
```
sudo bash -c 'cat > /etc/systemd/system/popcache.service << EOL
[Unit]
Description=POP Cache Node
After=network.target

[Service]
Type=simple
User=root
Group=root
WorkingDirectory=/opt/popcache
ExecStart=/opt/popcache/pop
Restart=always
RestartSec=5
LimitNOFILE=65535
StandardOutput=append:/opt/popcache/logs/stdout.log
StandardError=append:/opt/popcache/logs/stderr.log
Environment=POP_CONFIG_PATH=/opt/popcache/config.json

[Install]
WantedBy=multi-user.target
EOL'
```
### 8. Now run the node:
```
sudo systemctl enable popcache
sudo systemctl daemon-reload
sudo systemctl start popcache
```
## 🧩 Post-Installation
- Check if node is running:
```sudo systemctl status popcache```
![image](https://github.com/user-attachments/assets/49e31cb5-f57a-43c2-8f01-a8dd8775160b)

- Verify if your node is listening on ports 440 and 80:
```
ss -tulpn | grep -E ':80|:443'
```

- Monitoring the POP Cache Node:
Use the following cmd to use the following command to display your Node ID and other information.
```
curl -sk https://localhost/state && echo -e "\n"
```

- Use the following cmd to check health of your node
```
curl -sk https://localhost/health && echo -e "\n"
```

- Use [this dashboard](https://dashboard.testnet.pipe.network/node/xxxx) to view more information about your node.

> [!NOTE]
> Remember to replace your real data for the remaining data.

![image](https://github.com/user-attachments/assets/0509c592-67ed-4ef1-abc5-400878746975)
