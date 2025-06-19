# Proxmox Base Setup

## Additional Configuration (Post-Install)

### Enabled IOMMU for PCI Passthrough

To enable IOMMU support for passthrough:

1. **Edit GRUB config:**
   ```bash
   sudo nano /etc/default/grub
   ```

2. **Find the line:**
   ```bash
   GRUB_CMDLINE_LINUX_DEFAULT="quiet"
   ```

   Modify it depending on your CPU:
   - For **Intel CPUs**:
     ```bash
     GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on"
     ```
   - For **AMD CPUs**:
     ```bash
     GRUB_CMDLINE_LINUX_DEFAULT="quiet amd_iommu=on"
     ```

3. **Update GRUB and initramfs:**
   ```bash
   sudo update-grub
   sudo update-initramfs -u -k all
   ```

4. **Reboot the system:**
   ```bash
   sudo reboot
   ```

5. **Verify IOMMU is enabled:**
   ```bash
   dmesg | grep -e DMAR -e IOMMU
   ```

---

### Disabled Enterprise Repository

Proxmox ships with an enterprise APT repo that requires a subscription. To disable it:

```bash
sudo rm /etc/apt/sources.list.d/pve-enterprise.list
```

---

### Added Community (No-Subscription) Repository

Create or edit the file:

```bash
sudo nano /etc/apt/sources.list
```

Add the following line:

```bash
deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription
```

Then update package lists:

```bash
sudo apt update
```

---