# Loxone Smart Home Projects

> Eine Sammlung lokaler, nachvollziehbar dokumentierter Integrationen für Loxone, Raspberry Pi / DietPi und typische Smart-Home-Geräte.

Die Projekte in dieser Sammlung sind aus realen Installationen entstanden und verfolgen ein gemeinsames Ziel: Geräte und Dienste möglichst **lokal, transparent und robust** in Loxone einzubinden, statt unnötig zusätzliche Plattformen oder Cloud-Abhängigkeiten einzuführen.

Die Dokumentation ist derzeit überwiegend auf Deutsch. Issues und Pull Requests können auf Deutsch oder Englisch erstellt werden.

## Projekte

| Projekt | Release | Zweck |
| --- | --- | --- |
| [loxone-mhi-bridge](https://github.com/therealb4n4na/loxone-mhi-bridge) | `v3.3.0` | Lokale Integration von Mitsubishi Heavy Industries Klimageräten mit WF-RAC-Adaptern, inklusive passivem Polling, verifizierten Steuerbefehlen und Multi-Split-Konfliktlogik. |
| [loxone-bayrol-bridge](https://github.com/therealb4n4na/loxone-bayrol-bridge) | `v2.0.0` | BAYROL-Pooldaten für Loxone, lokaler Status-Cache sowie kontrollierte pH-Auto/Off-Steuerung über MQTT/WebSocket. |
| [loxone-km200-bridge](https://github.com/therealb4n4na/loxone-km200-bridge) | `v1.0.0` | Buderus/Bosch KM200 lokal auslesen, ausgewählte Werte schreiben und verifizieren sowie Warmwasser-/Heizungswerte historisieren. |
| [loxone-rpi-health](https://github.com/therealb4n4na/loxone-rpi-health) | `v1.3.0` | Raspberry-Pi-/DietPi-Systemzustand und zentrale Überwachung lokaler Smart-Home-Dienste für Loxone. |
| [loxone-desk-lamp-bridge](https://github.com/therealb4n4na/loxone-desk-lamp-bridge) | `v1.0.0` | Kleine lokale miIO-Bridge für Xiaomi/Yeelight-kompatible Schreibtischlampen mit Loxone-Lumitech-Ansteuerung. |

## Gemeinsame Grundsätze

### Local first

Wo technisch sinnvoll, läuft die Integration vollständig im lokalen Netzwerk. Cloud-Kommunikation wird nur verwendet, wenn das Zielsystem sie technisch benötigt.

### Lesen ist nicht Schreiben

Ein laufender Poller soll Geräte nicht nebenbei verändern. Statusabfragen und Steuerbefehle werden in den Projekten deshalb bewusst getrennt.

### Schreibbefehle werden verifiziert

Wenn eine Bridge einen Zustand verändert, reicht ein erfolgreich gesendeter HTTP-/MQTT-Befehl nicht automatisch als Beweis. Kritische Schreiboperationen werden nach Möglichkeit durch Rücklesen des tatsächlichen Zustands bestätigt.

### Loxone bleibt einfach

Komplexe Protokolle, Plausibilitätsprüfungen, Retries und Fehlerbehandlung gehören in die Bridge. Loxone soll möglichst wenige, klar definierte HTTP-Eingänge und Statuscodes benötigen.

### Fehler müssen diagnostizierbar sein

`systemd active` bedeutet nicht automatisch, dass das dahinterliegende Gerät erreichbar ist. Die Projekte unterscheiden deshalb soweit möglich zwischen:

- Prozess-/Dienststatus
- Geräte- oder Gateway-Erreichbarkeit
- Datenalter und Datenqualität
- erfolgreicher bzw. fehlgeschlagener Steuerung

### Keine Secrets in Git

Produktive Passwörter, Tokens, Geräteidentitäten, lokale State-Dateien, Logs und installationsspezifische Konfigurationen gehören nicht ins Repository. Dafür existieren Beispielkonfigurationen und `.gitignore`-Regeln.

## Typische Architektur

```text
Gerät / Gateway / Cloud
          │
          ▼
  Python-Bridge auf Linux
  ├─ Protokoll-/API-Logik
  ├─ Status-Cache
  ├─ Fehlerbehandlung
  ├─ Verifikation
  └─ HTTP-API
          │
          ▼
        Loxone
```

Die Bridges laufen typischerweise als `systemd`-Dienste auf DietPi/Debian. Ein Raspberry Pi reicht für viele dieser Integrationen problemlos aus.

## Versionierung

Die Projekte verwenden [Semantic Versioning](https://semver.org/):

```text
MAJOR.MINOR.PATCH
```

- **MAJOR** – inkompatible Änderungen an API, Konfiguration oder Verhalten
- **MINOR** – neue rückwärtskompatible Funktionen
- **PATCH** – Fehlerbehebungen und kleine rückwärtskompatible Änderungen

Stabile veröffentlichte Stände erhalten einen Git-Tag und einen GitHub Release. Der jeweilige `CHANGELOG.md` beschreibt die wesentlichen Änderungen.

## Installation

Es gibt bewusst keinen universellen Installer für alle Projekte. Die Geräte, Protokolle und Sicherheitsanforderungen unterscheiden sich zu stark.

Für ein Projekt daher immer zuerst dessen README lesen. Typisch ist:

```text
1. Repository klonen
2. Beispielkonfiguration kopieren und lokal anpassen
3. Python-Abhängigkeiten installieren
4. Bridge lokal testen
5. systemd-Dienst einrichten
6. HTTP-Endpunkte prüfen
7. erst danach Loxone konfigurieren
```

## Sicherheit

Diese Projekte sind für lokale Heim-/Gebäudenetze gedacht. Schreibende HTTP-Endpunkte sollten nicht direkt ins Internet veröffentlicht werden.

Wo möglich, werden Schreibzugriffe zusätzlich zur Netzwerk-Firewall auf eine konfigurierte Steuer-IP begrenzt. Für die jeweilige Implementierung gilt die `SECURITY.md` des Projekts.

## Beiträge und Erkenntnisse

Fehlerberichte, dokumentierte Gerätevarianten, Protokollbeobachtungen und Pull Requests sind willkommen. Besonders bei herstellerabhängigen oder reverse-engineerten Schnittstellen ist eine klare Trennung wichtig zwischen:

- **verifiziert** – am realen Gerät reproduzierbar bestätigt
- **experimentell** – plausibel, aber noch nicht ausreichend bestätigt
- **unbekannt** – beobachtet, Bedeutung noch offen

Damit sollen Erkenntnisse nachvollziehbar wiederverwendbar sein, anstatt nur eine Sammlung undokumentierter Werte oder „magischer“ IDs zu veröffentlichen.

## Umfang dieser Sammlung

Dieses Repository listet bewusst nur **allgemein nutzbare öffentliche Integrationen**. Private oder installationsspezifische Projekte werden hier nicht aufgeführt.

## Lizenz

Die hier verlinkten Softwareprojekte stehen, sofern im jeweiligen Repository nicht anders angegeben, unter der MIT License. Für Details gilt immer die `LICENSE`-Datei des jeweiligen Projekts.
