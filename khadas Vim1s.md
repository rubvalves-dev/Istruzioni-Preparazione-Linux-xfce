# Khadas VIM1S OS Installation Guide

## Requirements
- VIM1S Single Board Computer
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
1. Power off the VIM1S completely
2. Connect VIM1S to PC via USB-C cable
3. Enter Burning Mode:
   - Hold the POWER key and press RESET key
   - Continue holding POWER key for 2-3 seconds after pressing RESET
   - Release POWER key

### Using USB Burning Tool
1. Launch USB Burning Tool
2. Click `File -> Import Image` and select your OS image
3. Verify VIM1S is detected (should show as "Connected" in tool)
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

### Installazione TigerVNC
1. Aggiornare i repository e installare TigerVNC:
   ```bash
   sudo apt-get update
   sudo apt-get install tigervnc-scraping-server
   ```

2. Creare una directory .vnc nella home:
   ```bash
   mkdir -p ~/.vnc
   ```

3. Creare una password per la sessione VNC:
   ```bash
   vncpasswd
   ```

4. Avviare manualmente il server VNC:
   ```bash
   x0vncserver -passwordfile ~/.vnc/passwd -display :0 -localhost no -Log *:stderr:100 > ~/tigervnc-x0vncserver.log
   ```

### Creare il File di Servizio
1. Creare un nuovo file di servizio:
   ```bash
   sudo nano /etc/systemd/system/x0vncserver.service
   ```

2. Aggiungere il seguente contenuto al file:
   ```ini
   [Unit]
   Description=Remote desktop service (VNC)
   After=graphical.target

   [Service]
   Type=simple
   Environment=HOME=/home/khadas
   ExecStart=/usr/bin/x0vncserver -display :0 -passwordfile /home/khadas/.vnc/passwd -localhost no -Log *:stderr:100
   Restart=on-failure
   RemainAfterExit=yes

   [Install]
   WantedBy=multi-user.target
   ```

### Abilitare e Avviare il Servizio
1. Ricaricare i file di configurazione di systemd:
   ```bash
   sudo systemctl daemon-reload
   ```

2. Abilitare il servizio per l'avvio automatico:
   ```bash
   sudo systemctl enable x0vncserver.service
   ```

3. Avviare il servizio:
   ```bash
   sudo systemctl start x0vncserver.service
   ```

### Verificare lo Stato del Servizio
1. Controllare lo stato del servizio:
   ```bash
   sudo systemctl status x0vncserver.service
   ```

### Note Importanti
- Assicurarsi di sostituire `/home/khadas/.vnc/passwd` con il percorso corretto del file password se necessario
- Il servizio verrà avviato automaticamente al riavvio del sistema
- La porta predefinita per la connessione VNC è 5900
- Per accedere da remoto utilizzare: `indirizzo-ip:5900`

## References
- [Official Khadas Documentation](https://docs.khadas.com/products/sbc/vim1/install-os/install-os-into-emmc-via-usb-tool)
- [Khadas Download Center](https://dl.khadas.com/)
- [TigerVNC Documentation](https://tigervnc.org/)
