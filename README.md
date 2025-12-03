# PVE DHCP Recovery Script

> Automatically recover Proxmox VE (PVE) network connectivity using DHCP after being moved to a new network environment.

## 🧩 Overview

This script detects when a PVE host with a **statically configured IP** has lost network connectivity, and attempts to **recover access** by:
- Flushing existing IP addresses on `vmbr0`
- Requesting a temporary DHCP address
- Verifying connectivity (via `ping 8.8.8.8`)

This script is particularly helpful if your Proxmox VE host is moved to a new network and loses connectivity because its static IP configuration no longer matches the new environment. In such offsite scenarios, restoring network access can be difficult. By providing automatic, self-healing network recovery, these scripts make it much easier to regain remote access to your PVE host.

## ⚠️ Restrictions & Important Notes

- **No IP Conflict Detection:**  
  This script does *not* detect IP address conflicts. If another host on the network uses the same static IP as your PVE host, this script will not warn you or automatically resolve the collision.

- **Recommendation for Unknown Networks:**  
  If you are moving your PVE host to an unknown or unmanged network, it is recommended to intentionally set a static IP address that you know will *not* work (e.g., an unused or obviously incorrect address). This way, the DHCP-based recovery can safely obtain a working address without risking IP conflicts. Always allow DHCP to assign a suitable and conflict-free IP in unfamiliar network environments.

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
apt update
apt install isc-dhcp-client
```

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
cp check_network_and_request_dhcp.sh /usr/local/bin/
chmod +x /usr/local/bin/check_network_and_request_dhcp.sh
```

3. **Install systemd units:**

```bash
cp check-dhcp.service /etc/systemd/system/
cp check-dhcp.timer /etc/systemd/system/
systemctl daemon-reload
systemctl enable check-dhcp.timer
systemctl start check-dhcp.timer
```

4. **Check logs:**

```bash
journalctl -t vmbr0_dhcp_recover
```

## ✅ Tested On

- Proxmox VE 8.4.0
- Proxmox VE 9.0.3
- Proxmox VE 9.1.1

## 👤 Maintainer

Charles Shi  
📧 schrht@gmail.com

## 📝 License

[MIT](./LICENSE)
