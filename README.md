# PVE DHCP Recovery Script

> Automatically recover Proxmox VE (PVE) network connectivity using DHCP after being moved to a new network environment.

## 🧩 Overview

This script detects when a PVE host with a **statically configured IP** has lost network connectivity, and attempts to **recover access** by:
- Flushing existing IP addresses on `vmbr0`
- Requesting a temporary DHCP address
- Verifying connectivity (via `ping 8.8.8.8`)

It is especially useful for:
- Portable PVE hosts or test benches
- Remote environments where the static IP no longer works
- Remote recovery via Tailscale

## 📂 Files

| File | Description |
|------|-------------|
| `check_network_and_request_dhcp.sh` | Main script to run on the host |
| `check-dhcp.service` | systemd service to run the script once |
| `check-dhcp.timer` | systemd timer to run the script 5 minutes after boot |
| `LICENSE` | MIT License |
| `README.md` | This documentation |

## 🚀 Setup Instructions

1. **Install `dhclient`:**

This script depends on the **ISC DHCP Client** (`dhclient`).  
If not installed, run the following command on your Proxmox VE or Debian/Ubuntu system:

```bash
sudo apt update
sudo apt install isc-dhcp-client
````

You can verify installation with:

```bash
which dhclient
```

Expected output example:

```
/usr/sbin/dhclient
```

If this command shows “not found,” please ensure your `$PATH` includes `/sbin` and `/usr/sbin`.


2. **Install script:**

```bash
sudo cp check_network_and_request_dhcp.sh /usr/local/bin/
sudo chmod +x /usr/local/bin/check_network_and_request_dhcp.sh
```

3. **Install systemd units:**

```bash
sudo cp check-dhcp.service /etc/systemd/system/
sudo cp check-dhcp.timer /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable check-dhcp.timer
sudo systemctl start check-dhcp.timer
```

4. **Check logs:**

```bash
journalctl -t vmbr0_dhcp_recover
```

## ✅ Tested On

- Proxmox VE 8.4.0
- Proxmox VE 9.0.3

## 👤 Maintainer

Charles Shi  
📧 schrht@gmail.com

## 📝 License

[MIT](./LICENSE)
