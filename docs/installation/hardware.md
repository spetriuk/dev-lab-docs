# Hardware configuration

To prepare the machines for the automatic OS provisioning you should make some initial setup to enable required features.

## BIOS setup


### Common settings

- Enable Wake-on-LAN (WoL)
- Enable network boot (PXE Boot)
- Use UEFI mode and disable CSM (legacy) mode
- Disable secure boot

### Boot order options

1. SSD/HDD
2. PXE
3. USB

If no bootable operation system found on the main disk, then the computer should try to boot via network. After OS installation it will boot into it. 

Here is an example BIOS config (may be different on your machine):

```yaml
Devices:
  NetworkSetup:
    PXEIPv4: true
    PXEIPv6: false
Advanced:
  CPUSetup:
    VT-d: true
Power:
  AutomaticPowerOn:
    WoL: Automatic
Security:
  SecureBoot: false
Startup:
  CSM: false
```

## Network setup

- Connect the devices to the network (wired LAN connection). It should be the same network with initial controller machine.

- Bind a static IP address for each machine by it's MAC address (for example though the router web UI)
