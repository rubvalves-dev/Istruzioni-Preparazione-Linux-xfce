# Khadas VIM1 OS Installation Guide

## Requirements
- VIM1 Single Board Computer
- USB-C cable
- Windows PC
- [Khadas USB Burning Tool](https://dl.khadas.com/products/usb_burning_tool/USBBurningTool_v2.2.0.zip)
- OS Image from [Khadas Official Images](https://dl.khadas.com/)

## Installation Steps

### Prepare the Tools
1. Download and install the Khadas USB Burning Tool
2. Download your preferred OS image (e.g., Ubuntu)
3. Extract the downloaded image

### Installation Process
1. Power off the VIM1 completely
2. Connect VIM1 to PC via USB-C cable
3. Enter Burning Mode:
   - Hold the POWER key and press RESET key
   - Continue holding POWER key for 2-3 seconds after pressing RESET
   - Release POWER key

### Using USB Burning Tool
1. Launch USB Burning Tool
2. Click `File -> Import Image` and select your OS image
3. Verify VIM1 is detected (should show as "Connected" in tool)
4. Click `Start` to begin flashing
5. Wait for process to complete (progress bar will show 100%)
6. Click `Stop` when finished

### First Boot
1. Disconnect USB-C cable
2. Connect HDMI display and power supply
3. Press POWER button to boot
4. System will perform first-time setup

## Post-Installation
Follow the standard Linux configuration steps from the main README for:
- System updates
- Network configuration
- User setup

## References
- [Official Khadas Documentation](https://docs.khadas.com/products/sbc/vim1/install-os/install-os-into-emmc-via-usb-tool)
- [Khadas Download Center](https://dl.khadas.com/)