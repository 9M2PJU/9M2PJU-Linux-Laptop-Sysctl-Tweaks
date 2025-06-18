
# 🚀 Linux Laptop Sysctl Tweaks (Memory + Network)

Optimized `sysctl` configuration files for laptops using SSD, ZRAM, and WiFi connections. Tested on Debian-based systems with Intel Core i5 CPUs, 15 GiB RAM, and 200/100 Mbps network speed.

---

## 🧠 Memory Optimization (`99-laptop-memory.conf`)

Tuned for responsiveness, reduced SSD wear, better swap handling with ZRAM, and improved cache efficiency.

### 📂 `/etc/sysctl.d/99-laptop-memory.conf`

```ini
# Use ZRAM more often before disk swap
vm.swappiness=25

# Retain file system cache longer
vm.vfs_cache_pressure=50

# Improve SSD lifespan by reducing write frequency
vm.dirty_writeback_centisecs=1500
vm.dirty_expire_centisecs=3000
vm.dirty_background_ratio=5
vm.dirty_ratio=15

# Safe memory overcommit handling
vm.overcommit_memory=0
vm.overcommit_ratio=50

# Prevent early OOM killer (adjusted for 15 GiB RAM)
vm.min_free_kbytes=67584
````

Apply with:

```bash
sudo sysctl --system
```

---

## 🌐 Network Optimization (`99-laptop-network.conf`)

Optimized for 200 Mbps down / 100 Mbps up WiFi or Ethernet. Prioritizes low latency, congestion handling, and higher throughput using modern TCP algorithms.

### 📂 `/etc/sysctl.d/99-laptop-network.conf`

```ini
# Enable TCP Fast Open
net.ipv4.tcp_fastopen = 3

# Increase socket buffer sizes
net.core.rmem_max = 16777216
net.core.wmem_max = 16777216
net.core.rmem_default = 262144
net.core.wmem_default = 262144

# Queue and backlog tuning
net.core.netdev_max_backlog = 5000
net.core.somaxconn = 1024

# TCP memory tuning
net.ipv4.tcp_rmem = 4096 87380 16777216
net.ipv4.tcp_wmem = 4096 65536 16777216

# Use BBR for better congestion control over WiFi
net.core.default_qdisc = fq
net.ipv4.tcp_congestion_control = bbr

# Faster reuse of sockets
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_fin_timeout = 15

# TCP window scaling
net.ipv4.tcp_window_scaling = 1
```

Load BBR if needed:

```bash
sudo modprobe tcp_bbr
echo 'tcp_bbr' | sudo tee -a /etc/modules-load.d/modules.conf
sudo sysctl -w net.ipv4.tcp_congestion_control=bbr
```

---

## ✅ Tested On

* **CPU**: Intel Core i5-6300U
* **RAM**: 15 GiB with ZRAM
* **OS**: Debian (Testing)
* **Storage**: SSD
* **Network**: 200 Mbps WiFi down / 100 Mbps up

---

## 🛠️ Notes

* These tweaks are safe defaults for **laptop users with decent RAM and SSDs**.
* Adjust `vm.min_free_kbytes` based on your total RAM (`= RAM in MB × 4.5`).
* You can place both files in `/etc/sysctl.d/` and run `sudo sysctl --system` to activate.

---

## 📜 License

MIT — use freely and modify as needed.

```

---

Let me know if you'd like a matching `.conf` file structure for download (`sysctl.d/` folder), or want this converted to a **Gist** or **GitHub Action** for automated system tuning.
```
