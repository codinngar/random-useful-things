# How to install NVIDIA driver on fedora linux

`Make sure secure boot is enabled then disable it after the driver is installed`

1. Update system:
```bash
sudo dnf update
```

2. Reboot if an update is installed:
```bash
sudo reboot
```

3. Install the driver form the official website: [official NVIDIA drivers](https://www.nvidia.com/en-us/drivers/)

4. Install development tools and kernel headers:
```bash
sudo dnf install kernel-devel kernel-headers gcc make dkms acpid libglvnd-glx libglvnd-opengl libglvnd-devel pkgconfig
```

5. Disable nouveau drivers:
```bash
echo -e "blacklist nouveau\noptions nouveau modeset=0" | sudo tee /etc/modprobe.d/blacklist-nouveau.conf
```

6. Regenerate initramfs and reboot:
```bash
sudo dracut --force
```

7. Configure the system’s default target as “multi-user.target“:
```bash
sudo systemctl set-default multi-user.target
```

8. Reboot to enter a terminal screen:
```bash
sudo reboot
```

9. Navigate to the NVIDIA driver path:
```bash
cd ~/Downloads
```

10. Make the driver executable:
```bash
chmod +x NVIDIA-Linux-*.run
```

11. Install the driver:
```bash
sudo ./NVIDIA-Linux-*.run
```

12. Answer the prompts for your personal prefrence: `Mostly answer with yes`.

13. Enable GUI:
```bash
sudo systemctl set-default graphical.target
```

14. Reboot
```bash
sudo reboot
```

15. After rebooting the driver won't work then you should reboot then disable secure boot from bios then the driver will work.