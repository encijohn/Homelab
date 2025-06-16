# Proxmox Base Setup Guide

Initial setup procedures for Proxmox VE in a homelab environment.  
Date: 2025-06-16

---

## Initial Host Setup
- Installed Proxmox VE on bare metal
- Configured static IP for host
- Created non-root administrative user
- Verified DNS and gateway configuration
- Confirmed access to Proxmox Web UI

## Security Configuration
- Root account temporarily left enabled for emergency recovery
- Planned future disabling of root SSH access
- Non-root administrative user will switch to key-based SSH authentication

## Power Optimization
- Installed powertop:
  sudo apt install powertop -y

- Performed auto-tune:
  sudo powertop --auto-tune

- Created persistent systemd service:
  [Unit]
  Description=Powertop Auto-Tune
  After=multi-user.target

  [Service]
  Type=oneshot
  ExecStart=/usr/sbin/powertop --auto-tune

  [Install]
  WantedBy=multi-user.target

- Enabled the service:
  sudo systemctl enable powertune.service

## System Fixes
- Fixed dpkg lock issue:
  sudo dpkg --configure -a

- Accepted to keep existing SSH configuration during package upgrade

## Networking
- Verified internet access
- Checked gateway and DNS settings to ensure external connectivity

---

## Next Steps
- Create initial VM for storage (Ubuntu Server)
- Pass through physical drives as required
- Install MergerFS and configure Samba/NFS
