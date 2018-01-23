# Création d'écrans virtuels pour NVidia

# Create dummy screens

We will update the current xorg.conf

```bash
sudo nvidia-xconfig -a --allow-empty-initial-configuration --cool-bits=28 --use-display-device="DFP-0" --connected-monitor="DFP-0"
```

## Verification

Displaying the file `/etc/X11/xorg.conf`, you should see blocks with the following information :
```
cat /etc/X11/xorg.conf
```

```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "GeForce GTX 1060 3GB"
    BusID          "PCI:1:0:0"
    Option         "Coolbits" "28"
EndSection
```
