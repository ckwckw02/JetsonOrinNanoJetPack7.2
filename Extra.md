# Update the L4T to 39.2

https://developer.nvidia.com/embedded/jetpack/downloads

```bash
wget https://developer.nvidia.com/downloads/embedded/L4T/r39_Release_v2.0/release/Jetson_Linux_R39.2.0_aarch64.tbz2
tar xf Jetson_Linux_R39.2.0_aarch64.tbz2
cd Linux_for_Tegra/
sudo ./tools/l4t_flash_prerequisites.sh
```

**Jetson Orin Nano REC mode**

```bash
sudo ./flash.sh --no-systemimg -c bootloader/generic/cfg/flash_t234_qspi.xml jetson-orin-nano-devkit-super internal
```

After a few minutes, you will see:

```
*** The target generic has been flashed successfully. ***
```

---

# Install VNC Server

```bash
sudo apt update
sudo apt install x11vnc -y
x11vnc -storepasswd
```

## Configure GDM3 Auto Login

Edit `/etc/gdm3/custom.conf`:

```bash
sudo vi /etc/gdm3/custom.conf
```

Press `i` to edit, then uncomment and change:

**FROM:**

```
# AutomaticLoginEnable = true
# AutomaticLogin = user
```

**TO:**

```
AutomaticLoginEnable = true
AutomaticLogin = jetson
```

Press `ESC` + `:wq` to save and exit.

## Create systemd Service

Edit `/etc/systemd/system/x11vnc.service`:

```bash
sudo vi /etc/systemd/system/x11vnc.service
```

Press `i`, then paste the following:

```ini
[Unit]
Description=Start x11vnc at startup
After=display-manager.service
Requires=display-manager.service

[Service]
Type=simple
User=jetson
ExecStart=/usr/bin/x11vnc \
    -display :0 \
    -auth guess \
    -forever \
    -loop \
    -noxdamage \
    -repeat \
    -rfbauth /home/jetson/.vnc/passwd \
    -rfbport 5900 \
    -shared

Restart=always
RestartSec=3

[Install]
WantedBy=graphical.target
```

Press `ESC` + `:wq` to save and exit.

## Reload systemd and Enable Service

```bash
# Reload the systemd manager configuration
sudo systemctl daemon-reload

# Enable the service to start on boot
sudo systemctl enable x11vnc.service

# Start the service immediately
sudo systemctl start x11vnc.service

# Reboot the device
sudo reboot
```

You can now use UltraVNC Viewer or any other VNC client to connect.
