# Proxmox Homelab Changelog
> Initial Setup - Proxmox VE Base Configuration  
> Date: 2025-06-16

---

# Initial Host Setup
- Installed Proxmox VE on bare metal (i7 7700, 32GB RAM, 128GB NVME, 9TB HDD)
- Set static IP
- Configured a primary PVEAdmin User
- Verified Local DNS, gateway, and Proxmox UI access

# Security Config
- Left `root` user enabled temporarily for recovery/debugging
- Planning to disable `root` SSH once setup stabilizes
- Will later switch to key-based SSH login for PVEAdmin User

## Power Optimization
- Installed `powertop`:
  ```bash
  sudo apt install powertop -y
  ```
- Tuned system with:
  ```bash
  sudo powertop --auto-tune
  ```
- Created persistent service on /etc/systemd/system:
  ```bash ini
  touch /etc/systemd/system/powertop.service
  nano /etc/systemd/system/powertop.service

  # I then added this code to auto-start powertop service after reboot

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
  ```bash
  sudo systemctl start powertune.service
  ```

## 🔧 System Fixes
- Resolved `dpkg` interrupted error with:
  ```bash
  sudo dpkg --configure -a
  ```
- Chose to keep existing config when prompted during SSH package upgrade

## Networking
- Verified internet connectivity
- Confirmed correct default gateway 
- DNS resolution tested and restored

---

## Next Milestone
- Create Ubuntu Server VM for NAS
- Pass through storage drives
- Install MergerFS + Samba/NFS
