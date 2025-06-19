# Proxmox Base Setup 

This guide outlines the initial configuration and post-install setup of the Proxmox Server intended for the *homelab* environment I personally run. 

# Access the Web UI

- Open a browser and go to `https://<proxmox-ip>:8006`
- Login as `root` user or `admin` user and use the `password` you set on installation.

***- Most of the setup can be done on Proxmox GUI. But for this setup, I'll do most things through CLI since I'm targeting to learn more about the inner workings of the Debian system that Proxmox runs on.*** 

# 1. Create a Admin User

For what its worth, it is best practice that we do not run anything on `root`. 

```bash
#Create user(replace 'yourpassword')
pveum user add yourusername@pam --password yourpassword

#Grant administrator privileges
pveum aclmod / -user yourusername@pam -role Administrator
```

# 2. Disable Enterprise Subscription Nag

For most home users, we do not need to use most if not all of the Enterprise features. 

To remove the `nag` popup shown to non-subscription users every login, you can copy and paste this on proxmox shell:

```bash
sudo sed -i.bak "s|data.status.nodename|data.status.nodename || true|g" /usr/share/javascript/proxmox-widget-toolkit/proxmoxlib.js
```


# 3. Disable Enterprise Repository and Add Community Repository

Proxmox ships with an enterprise APT repo that requires a subscription hence the `nag`. To disable it:
```bash
sudo rm /etc/apt/sources.list.d/pve-enterprise.list
```

`Add community repo:`
```bash
sudo rm /etc/apt/sources.list.d/pve-enterprise.list

#Add the following line at the bottom 
deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription

#then update package lists
sudo apt update
```

# 4. Install Powertop | Tune Proxmox For Lower Power Consumption 

Even if it runs on mostly on idle, *a watt saved, is a bill we don't pay*. 

`Install Powertop:`
```bash
sudo apt install powertop -y

```

`Create a systemd service to run auto-tune on start:`
```bash
sudo nano /etc/systemd/system/powertop.service

#inside the file
[Unit]
Description=Powertop tunings
After=multi-user.target

[Service]
Type=idle
ExecStart=/usr/sbin/powertop --auto-tune
RemainAfterExit=true

[Install]
WantedBy=multi-user.target
```

`Enable the service:`
```bash
sudo systemctl daemon-reexec
sudo systemctl enable powertop
sudo systemctl start powertop
```


# 5. Enable IOMMU for PCI Passthrough (*if your system supports it*)

`Edit GRUB config:`
```bash
sudo nano /etc/default/grub

#Find this line, uncomment it or add it

GRUB_CMDLINE_LINUX_DEFAULT="quiet intel"

# For Intel processors:
GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on"

# For AMD:
GRUB_CMDLINE_LINUX_DEFAULT="quiet amd_iommu=on"
```

`Update GRUB and initramfs:`
```bash
sudo update-grub
sudo update-initramfs -u -k all
sudo reboot
```

`To verify:`
```bash
dmesg | grep -e DMAR -e IOMMU
```


# 6. (Optional) Storage Passthrough | Create a Simple Storage/NAS VM inside Proxmox

This will be using *Ubuntu Server 24.04* *minimal* as base image. This guide assumes you have installed the VM/Template already. 

Prerequisites:
- Disks must **not be mounted** or used by the host Proxmox OS.
- Make sure you know the **device paths** (`/dev/sdX` or `/dev/disk/by-id/...`).

**- List Available Disks**
```bash
lsblk -o NAME,SIZE,TYPE,MOUNTPOINT
```

**- (Recommended) Use Disk by ID**
This makes device paths persistent across reboots.
```bash
ls -l /dev/disk/by-id/

#Look for entries like: 
ata-ST4000DM004-XXXXXXX -> ../../sdb

#Copy the full path, e.g.:
/dev/disk/by-id/ata-ST4000DM004-XXXXXXX
```

**- Attach Disks to VM**
`In proxmox shell`
```bash
qm set <VMID> -virtioX /dev/disk/by-id/<your_disk_id>

#Example: 
qm set 100 -virtio1 /dev/disk/by-id/ata-ST4000DM004-XXXXXXX
```
	`virtio1` means you're adding it as the second drive. Use `virtio0` for the first if needed.

**- To verify that it is successfully passed through, type in Proxmox console:**
```bash
qm config 100
```

And with that, we can now launch our  very first VM!
