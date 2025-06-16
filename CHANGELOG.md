# 📘 Proxmox Homelab Changelog
> 🗓️ Initial Setup - Proxmox VE Base Configuration  
> 👤 Admin User: `adminenci@pam`  
> 🗂️ Date: 2025-06-16

---

## ✅ Initial Host Setup
- Installed Proxmox VE on bare metal
- Set static IP: `192.168.36.21`
- Configured primary user: `adminenci@pam`
- Verified DNS, gateway, and Proxmox UI access

## 🛡️ Security Config
- Left `root` user enabled temporarily for recovery/debugging
- Planning to disable `root` SSH once setup stabilizes
- Will later switch to key-based SSH login for `adminenci`

## ⚡ Power Optimization
- Installed `powertop`:
  ```bash
  sudo apt install powertop -y
  ```
- Tuned system with:
  ```bash
  sudo powertop --auto-tune
  ```
- Created persistent service:
  ```ini
  [Unit]
  Description=Powertop Auto-Tune
  After=multi-user.target

  [Service]
  Type=oneshot
  ExecStart=/usr/sbin/powertop --auto-tune

  [Install]
  WantedBy=multi-user.target
  ```

- Enabled with:
  ```bash
  sudo systemctl enable powertune.service
  ```

## 🔧 System Fixes
- Resolved `dpkg` interrupted error with:
  ```bash
  sudo dpkg --configure -a
  ```
- Chose to keep existing config when prompted during SSH package upgrade

## 🛜 Networking
- Verified internet connectivity
- Confirmed correct default gateway (`192.168.36.1`)
- DNS resolution tested and restored

---

## ⏭️ Next Milestone
- Create Ubuntu Server VM for NAS
- Pass through storage drives
- Install MergerFS + Samba/NFS
