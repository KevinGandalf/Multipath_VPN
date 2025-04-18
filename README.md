![MPVPN Logo](mpvpn_logo_klein.png)

## Installationsscript:
```bash
#Ubuntu / Debian
sudo apt install wget && sudo bash -c "$(wget -qO- https://kevingandalf.github.io/mpvpn/mpvpn-install)"

#Almalinux, RHEL, Rocky, CentOS etc.
dnf install -y wget && sudo bash -c "$(wget -qO- https://kevingandalf.github.io/mpvpn/mpvpn-install)"
```

[![Join our Discord](https://img.shields.io/badge/Discord-Join%20the%20Community-blue?style=for-the-badge)](https://discord.gg/qXRzXvzJQM)


# mpvpn

**mpvpn** ist ein flexibles, modulares System zur dynamischen Verwaltung und Nutzung mehrerer VPN-Gateways parallel. Es wurde entwickelt, um Datenschutz, Ausfallsicherheit und Routing-Kontrolle auf ein neues Level zu bringen – ideal für Umgebungen die den Einsatz verschiedener VPN Anbieter benötigen.

Funktionen im Überblick:
- Multipath-Routing: Nutzt mehrere VPN-Verbindungen gleichzeitig über iproute2, mit automatischem Failover und dynamischer Gewichtung.
- Loadbalancing des gesamten Traffics über alle VPN Verbindungen
- VPN-Gateway-Überwachung: Permanente Prüfung der Verbindungen via ICMP (Ping), automatische Deaktivierung fehlerhafter Pfade aus der Routingtabelle.
- Killswitch-Integration: Aktiviert iptables-Regeln, um bei VPN-Ausfall jeglichen Traffic zu blockieren – schützt zuverlässig vor Datenlecks.
- Split-DNS

UPCOMING:
- Stealthmode: Schützt deine Privatsspähre auch in Fällen von direkter Kompromittierung - keine Nachvollziehbaren Daten ohne Zugriff auf den LUKS Container.
  - Ablage der gesamten Configs in einem verschlüsselten LUKS Container
  - Manuelles Mouting zum Start von MPVPN notwendig
  - Automatischer Unmount nachdem alle notwendigen Scripte ausgeführt und Verbindungen initiiert worden sind
  - Deaktivierung aller relevanten Systembefehle wie ip route, iptables etc. nach der Aktivierung des Stealthmodes
  - Systemweites Logging wird deaktiviert
    
    [https://github.com/KevinGandalf/mpvpn-mod-stealth](https://github.com/KevinGandalf/mpvpn-mod-stealth)

- Streaming-Modus: Selektives Routing basierend auf Ziel-ASNs oder Diensten (z. B. Netflix, Gaming), gesteuert über konfigurierbare Regeln.
  - DAITA-Kompatibilität: Routing einzelner Domains oder Dienste gezielt über bestimmte VPNs – automatisch und lernfähig.
    [https://github.com/KevinGandalf/mpvpn/tree/testing/beta](https://github.com/KevinGandalf/mpvpn/tree/testing/beta)

## Anforderungen

Alle Variablen werden in der `globals.sh` definiert. Die Anforderungen sind in der `requirements.sh` definiert – dieses Script muss einmalig ausgeführt werden.

Es installiert folgende Pakete:
- curl
- wget
- net-tools
- wireguard
- Bei Bedarf auch OpenVPN

Alle relevanten Start-Skripte befinden sich im Verzeichnis `/opt/mpvpn/helperscripts/startup`.

## Installation
1. Ein paar dinge vorab erledigen und klonen des Repository:
Das Script führt komfortabel durch den gesamten Installationsprozes. Im Anschluss ist es möglich Wireguard und OpenVPN Konfiguration per Drag&Drop hinzuzufügen.

```bash
#Ubuntu / Debian
sudo apt install wget && sudo bash -c "$(wget -qO- https://kevingandalf.github.io/mpvpn/mpvpn-install)"

#Almalinux, RHEL, Rocky, CentOS etc.
dnf install -y wget && sudo bash -c "$(wget -qO- https://kevingandalf.github.io/mpvpn/mpvpn-install)"
```
oder

```bash
sudo apt update && sudo apt upgrade -y && sudo apt install -y sudo git curl wget
cd /opt
#Root Passwort festlegen
sudo passwd
su
git clone https://github.com/KevinGandalf/mpvpn
```

2. Mache das Script ausführbar:
    ```bash
    cd /opt/mpvpn
    find /opt/mpvpn -type f -name "*.sh" -exec chmod +x {} \;
    ln -s /opt/mpvpn/helperscripts/assets/menu.sh /usr/local/bin/mpvpn
    mpvpn --install
    ```

3. Die Basis-Konfigurationen befinden sich in der Datei `globals.sh`. Die Konfiguration der zu verwendenden VPN-Verbindungen und Routing-Tabellen erfolgt hier.

4. Wireguard (und ggf. OpenVPN Verbindung) Verbindung per Drag&Drop hinzufügen(z.B. per Putty!):
    ```bash
    mpvpn --addwg
    #für OpenVPN
    mpvpn --addovpn
    ```

## Konfiguration

Der Übersicht halber wurde das Main Script `mpvpn.sh` aufgeräumt und die Sequenzen in einzelne Scripte verpackt. Es gibt mehrere Variablen, die angepasst werden müssen:

### Folgende Variablen müssen ggf. angepasst werden:

```bash
# Basisverzeichnis für VPN-Skripte
BASE_PATH="/opt/mpvpn"

# Standard LAN Interface
DEFAULT_LANIF="enp1s0"

# Standard Gateway
DEFAULT_WANGW="192.168.1.1"

# WireGuard Konfigurationsverzeichnis
WG_CONF_DIR="/etc/wireguard"
# OpenVPN Konfigurationsverzeichnis
OVPN_CONF_DIR="/etc/openvpn"

# WireGuard Konfigurationsnamen (entsprechen den .conf-Dateien in /etc/wireguard)
WGVPN_LIST=("vpn1" "vpn2" "vpn3" "vpn4")

# Beispiel:
# WGVPN_LIST=("mullvad" "ovpn" "azirevpn" "surfshark")

# OpenVPN Konfigurationen
ENABLE_OVPN=false
# Default: ENABLE_OVPN=false
OVPN_LIST=("vpn5" "vpn6")
```    
5. MPVPN starten:
    ```bash
    mpvpn --startmpvpn
    ```

## mpvpn Befehle
```bash
    Verfügbare Optionen für mpvpn:
      --startmpvpn : Startet MPVPN
      --install    : Installiert die Abhängigkeiten
      --addwg      : Neue WireGuard-Verbindung hinzufügen.
      --addovpn    : Neue OpenVPN-Verbindung hinzufügen.
      --list       : Alle Verbindungen anzeigen.
      --help       : Zeigt diese Hilfe an.
      --version    : Gibt die Version des Skripts aus.
```

