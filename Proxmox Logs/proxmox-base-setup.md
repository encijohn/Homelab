# Proxmox Homelab Changelog
Initial Setup - Proxmox VE Base Configuration  
Date: 2025-06-16

---

## Initial Host Setup
- Installed Proxmox VE on bare metal
- Set static IP for host
- Configured primary non-root admin user
- Verified DNS, gateway, and Proxmox UI access

## Security Configuration
- Root user left enabled temporarily for recovery/debugging
- Plan to disable root SSH login after setup stabilizes
- Will later switch to key-based SSH login for administrative user

## Power Optimization
- Installed powertop:
  sudo apt install powertop -y

- Tuned system with:
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
- Resolved dpkg interrupted error with:
  sudo dpkg --configure -a

- Retained existing SSH configuration during package upgrade to avoid service disruption

## Networking
- Verified internet connectivity
- Confirmed default gateway and DNS resolution operational

---

## Next Milestone
- Create Ubuntu Server VM for NAS
- Pass through storage drives
- Install MergerFS and configure Samba or NFS
