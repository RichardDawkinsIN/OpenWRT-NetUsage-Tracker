# NetUsage — OpenWrt Router Data Usage Dashboard

A lightweight LuCI page for OpenWrt that shows **total download / upload / combined data usage** of your router, with a modern card-based UI. Data is collected using `vnstat` and displayed under **Status → Data Usage**, auto-refreshing every 10 seconds.

Built for OpenWrt **23.05 / 24.10 / 25.x** (`apk`-based systems, no Lua runtime required) — uses the modern JSON menu + `rpcd` backend + client-side JS view architecture.

![OpenWrt](https://img.shields.io/badge/OpenWrt-23.05%2B-blue) ![Shell](https://img.shields.io/badge/script-POSIX%20sh-lightgrey) ![License](https://img.shields.io/badge/license-MIT-green)

## ✨ Features

- 📊 Total download, total upload, and combined usage — shown as gradient cards
- 📅 Last 6 months usage table
- 🔄 Auto-refresh every 10 seconds, no page reload needed
- 🔍 Auto-detects your WAN interface (works with DHCP, PPPoE, etc.)
- 💾 Persistent history across reboots (backed by `vnstat`)
- 🧩 Works with both `apk` (OpenWrt 23.05+) and `opkg` (older releases)

## 📋 Requirements

- OpenWrt router with internet/SSH access
- Free space for the `vnstat` package (~100 KB)
- Admin (root) access via SSH

## 🚀 Installation

1. SSH into your router:
   ```bash
   ssh root@<router-ip>
   ```

2. Download the script:
   ```bash
   wget https://github.com/RichardDawkinsIN/OpenWRT-NetUsage-Tracker/releases/latest/download/netusage.sh
   ```

3. Make it executable and run it:
   ```bash
   chmod +x netusage.sh
   ./netusage.sh
   ```

4. Open your router's web UI and go to **Status → Data Usage**.

> On a fresh install, `vnstat` needs a little time to start collecting data — numbers will be low/zero at first and grow as traffic passes through.

## 🗑️ Uninstall

```bash
rm -f /usr/libexec/rpcd/netusage
rm -f /usr/share/rpcd/acl.d/luci-app-netusage.json
rm -f /usr/share/luci/menu.d/luci-app-netusage.json
rm -f /www/luci-static/resources/view/netusage.js
rm -f /etc/config/netusage
/etc/init.d/rpcd restart
rm -f /tmp/luci-indexcache* /tmp/luci-modulecache/*
/etc/init.d/uhttpd restart
```

(Optionally also remove vnstat: `apk del vnstat` or `opkg remove vnstat`, and delete `/etc/vnstat` if you no longer want the collected history.)

## 🛠️ How it works

| Component | Path | Purpose |
|---|---|---|
| Config | `/etc/config/netusage` | Stores which WAN interface to monitor |
| Backend | `/usr/libexec/rpcd/netusage` | rpcd plugin script that runs `vnstat --json` and returns it over ubus |
| ACL | `/usr/share/rpcd/acl.d/luci-app-netusage.json` | Grants the LuCI session permission to call the ubus method |
| Menu | `/usr/share/luci/menu.d/luci-app-netusage.json` | Registers "Data Usage" under the Status menu |
| Frontend | `/www/luci-static/resources/view/netusage.js` | Client-side JS view that renders the dashboard and polls for updates |

## ⚠️ Notes

- If your router uses `opkg` instead of `apk`, the script auto-detects and uses whichever is available.
- The script safely removes any leftover files from older Lua-based LuCI installs before setting up the modern version.
- Re-running the script is safe — it will reconfigure everything from scratch.

## 📄 License

MIT — free to use, modify, and share.
