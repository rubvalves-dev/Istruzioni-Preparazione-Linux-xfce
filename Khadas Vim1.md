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

## Configurazione Avvio Automatico Browser

### Creazione Script di Avvio
1. Creare il file script:
   ```bash
   nano ~/start_browser.sh
   ```

2. Aggiungere il seguente contenuto allo script:
   ```bash
   #!/bin/bash
   chromium-browser --start-maximized --kiosk http://indirizzo-desiderato.com
   ```

3. Rendere lo script eseguibile:
   ```bash
   chmod +x ~/start_browser.sh
   ```

### Configurazione Autostart
1. Creare la directory autostart se non esiste:
   ```bash
   mkdir -p ~/.config/autostart
   ```

2. Creare il file desktop per l'avvio automatico:
   ```bash
   nano ~/.config/autostart/start_browser.desktop
   ```

3. Inserire il seguente contenuto nel file .desktop:
   ```ini
   [Desktop Entry]
   Type=Application
   Exec=/home/khadas/start_browser.sh
   Hidden=false
   NoDisplay=false
   X-GNOME-Autostart-enabled=true
   Name[en_US]=Start Browser
   Name=Start Browser
   Comment=Avvia il browser in modalità kiosk
   ```

4. Salvare il file e impostare i permessi:
   ```bash
   chmod +x ~/.config/autostart/start_browser.desktop
   ```

### Note Importanti
- Sostituire `http://indirizzo-desiderato.com` con l'URL effettivo
- L'opzione `--kiosk` avvia il browser in modalità a schermo intero
- L'opzione `--start-maximized` assicura che la finestra sia massimizzata

## Configurazione Autologin

### Configurazione LightDM
1. Aprire il file di configurazione di LightDM:
   ```bash
   sudo nano /etc/lightdm/lightdm.conf
   ```

2. Aggiungere le seguenti righe nella sezione [Seat:*]:
   ```ini
   [Seat:*]
   autologin-user=khadas
   autologin-user-timeout=0
   user-session=xfce
   greeter-session=lightdm-gtk-greeter
   ```

3. Salvare il file (Ctrl+O, poi Ctrl+X)

### Verifica Configurazione
1. Riavviare il sistema:
   ```bash
   sudo reboot
   ```

2. Il sistema dovrebbe ora eseguire l'accesso automatico con l'utente 'khadas'

### Note Importanti
- Sostituire `khadas` con il nome utente desiderato se diverso
- Assicurarsi che l'utente specificato esista nel sistema
- La configurazione dell'autologin dovrebbe essere eseguita prima della configurazione dell'avvio automatico del browser

## Configurazione VNC Server

### Installazione x11vnc
1. Aggiornare i repository e installare x11vnc:
   ```bash
   sudo apt update
   sudo apt install x11vnc
   ```

2. Configurare x11vnc:
   ```bash
   # Creare la directory .vnc e impostare la password
   mkdir -p ~/.vnc
   x11vnc -storepasswd /home/khadas/.vnc/passwd

   # Avviare x11vnc
   x11vnc -display :0 -auth /var/run/lightdm/root/:0
   ```

3. Configurare il firewall:
   ```bash
   sudo ufw allow 5900/tcp
   ```

### Configurazione del Servizio
1. Creare il file di servizio systemd:
   ```bash
   sudo nano /etc/systemd/system/x11vnc.service
   ```

2. Inserire il seguente contenuto:
   ```ini
   [Unit]
   Description=Start x11vnc at startup
   After=multi-user.target

   [Service]
   Type=simple
   ExecStart=/usr/bin/x11vnc -auth guess -forever -loop -noxdamage -repeat -rfbauth /home/khadas/.vnc/passwd -rfbport 5900 -shared -o /var/log/x11vnc.log

   [Install]
   WantedBy=multi-user.target
   ```

### Attivazione del Servizio
1. Ricaricare la configurazione di systemd:
   ```bash
   sudo systemctl daemon-reload
   ```

2. Abilitare il servizio per l'avvio automatico:
   ```bash
   sudo systemctl enable x11vnc.service
   ```

3. Avviare il servizio:
   ```bash
   sudo systemctl start x11vnc.service
   ```

4. Verificare lo stato del servizio:
   ```bash
   sudo systemctl status x11vnc.service
   ```

### Note Importanti
- Assicurarsi di sostituire `/home/khadas/.vnc/passwd` con il percorso corretto del file password
- Il servizio verrà avviato automaticamente al riavvio del sistema
- La porta predefinita per la connessione VNC è 5900
- Per accedere da remoto utilizzare: `indirizzo-ip:5900`

## References
- [Official Khadas Documentation](https://docs.khadas.com/products/sbc/vim1/install-os/install-os-into-emmc-via-usb-tool)
- [Khadas Download Center](https://dl.khadas.com/)