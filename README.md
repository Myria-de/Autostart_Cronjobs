# Autostart_Cronjobs

**Befehlszeilen aus der LinuxWelt 2025-03**

**„Startprogramme“ unter Ubuntu und Mint**
```
sh -c "sleep 30; [Programm]"
```
```
X-GNOME-Autostart-enabled=false
```
```
cd /etc/xdg/autostart/
sudo sed --in-place 's/NoDisplay=true/NoDisplay=false/g' *.desktop
```

**Systemd-Dienste kontrollieren**
```
systemctl
```
```
systemd-analyze plot > systemd.svg
```
```
systemctl --user list-unit-files --state=enabled
```
**Tipp:** Das Tool Systemd Manager ermöglicht die Verwaltung von Systemd-Units in einer grafischen Oberfläche. Sie können die Unit-Dateien bearbeiten, Protokolle betrachten und Dienste starten oder stoppen. Das Programm liegt nur im Quelltext vor. Um es zu erstellen, installieren Sie zuerst Rust (siehe https://rustup.rs). Laden Sie von https://github.com/GuillaumeGomez/systemd-manager die ZIP-Datei mit dem Quellcode herunter (unter „Code“), die Sie entpacken. Danach starten Sie das Script „install.sh“.

**Systemd für Autostarts nutzen**
```
# start_script.service
[Unit]
Description=Demo-Script starten
[Service]
Type=exec
Restart=no
ExecStart=%h/Scripts/demo_script.sh
[Install]
WantedBy=graphical-session.target
```
```
#!/usr/bin/env bash
#demo_script.sh
SCRIPT_PATH="$(dirname -- "${BASH_SOURCE[0]}")"
SCRIPT_PATH="$(cd -- "$SCRIPT_PATH" && pwd)" 
echo $(date) >> $SCRIPT_PATH/demo_script.txt
```
```
chmod +x ~/Scripts/demo_script.sh
systemctl --user enable start_script.service
systemctl --user start start_script.service
```
Wenn Sie den Dienst nicht mehr nutzen wollen, deaktivieren Sie ihn mit
```
systemctl --user disable start_script.service
```
Danach löschen Sie die Servicedatei.

**Systemd für Starts nach Zeitplan verwenden**
```
#backup.service
[Unit]
Description=Start Backup-Script
[Service]
Type=oneshot
#Pfad anpassen
ExecStart=/opt/backup.sh
```
```
#backup.timer
[Unit]
Description=Timer unit for backup.service
[Timer]
Unit=backup.service
#täglich um 18:00
OnCalendar=*-*-* 18:00:00
Persistent=true
[Install]
WantedBy=timers.target
```
```
#!/usr/bin/env bash
#backup.sh
DATE=$(date +%Y-%m-%d-%H%M%S)
BACKUP_DIR="[Backup-Ordner]/"
SOURCE="/home/[Name]/Dokumente"
tar -cvzpf $BACKUP_DIR/backup-[Name]-$DATE.tar.gz $SOURCE
```
```
systemctl daemon-reload
systemctl enable backup.timer
systemctl start backup.timer
```
**Zeitplan für Cron konfigurieren**
```
EDITOR=gnome-text-editor crontab -e
```
```
EDITOR=gnome-text-editor sudoedit /etc/crontab
```
```
0 22 * * * rsync -av /home/sepp/ /media/sepp/USB/backup
```
Auf Desktopsystemen mit grafischer Oberfläche lässt sich das manuelle Editieren der Crontab mit dem Tool gTock vermeiden (https://github.com/rizalmart/gtock). Es bietet ein grafisches Frontend, das das Anlegen von Cronjobs erheblich erleichtert. Zur Installation starten Sie die folgenden sechs Befehlszeilen im Terminal:
```
sudo apt install cron git
mkdir ~/src
cd ~/src
git clone https://github.com/rizalmart/gtock.git –depth=1
cd gtock
./install.sh
```
Bestätigen Sie mit *yes* und der Enter-Taste. Danach können Sie das Tool im Terminal mit
gtock starten oder über eine Suche nach „Geplante Aufgaben“. Die wichtigsten Intervalle wie „Every Day“ oder „Every month“ finden Sie klickfertig vor, und „Preview“ (Vorschau) bietet in der Form „On every day at 22:00“ eine gute Kontrolle.

**Scripts mit Cron beim Systemstart ausführen**
```
@reboot [/Pfad/Script]
```
```
@reboot sleep 60 && [/Pfad/Script]
```
**Die Konfiguration mit .bashrc und .profile**
```
alias d='cd ~/Downloads'
```
```
source ~/.bashrc
```
