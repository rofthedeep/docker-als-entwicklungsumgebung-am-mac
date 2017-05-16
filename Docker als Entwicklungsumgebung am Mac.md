# Docker als Entwicklungsumgebung am Mac

## Problem und Ziel

Mamp / Lamp Entwicklungsumgebung war nicht mehr flexibel genug. Gerade bei der Entwicklung mit verschiedenen PHP Versionen kam es immer wieder zu Problemen.

**Ziel:** flexible lokale Entwicklungsumgebung, die je nach Projekt dessen Anforderungen erfüllt.

## Anforderungen

- **einfaches "hochfahren"**: Entwicklungsumgebungen sollten schnell verfügbar sein
- **vhosts**: "dev.website.de" muss auch weiterhin funktionieren (Lizenzen etc.)
- **SSL-Zertifikate**: Z.B. für Entwicklung und Tests von Payment Schnittstellen
- **schnelle Einrichtung**: die Einrichtung neuer Projekte muss schnell gehen
- **Microservice Architektur** soll möglich sein 
- **Performance**: Ladezeiten sollen im Rahmen bleiben
- **dev = stage = live**: Die Entwicklungsumgebung soll mit der gleichen Struktur laufen, wie auch die Liveumgebung

## Umsetzung

### einfaches hochfahren

Für Mac gibt es die **Docker App**. Diese kann unter [https://docs.docker.com/docker-for-mac/](https://docs.docker.com/docker-for-mac/) heruntergeladen werden.

**Docker Compose** ist eine Konfigurationsdatei für Docker Container. Über die Kommandozeile können so alle Container mit Hilfe eines Befehls `docker-compose up -d` gestartet werden.

Danach ist die Entwicklungsumgebung verfügbar und kann verwendet werden.

### vhosts

Lösung: [https://github.com/jwilder/nginx-proxy](https://github.com/jwilder/nginx-proxy)

- Proxy, der als Container gestartet wird (Auch einfach über `docker-compose up -d`)
- dieser leitet Einträge aus der Hosts Datei an entsprechende Container weiter
- die dev URL muss in der docker-compose Datei des Projektes eingetragen werden

### Microservice Architektur

**Docker** ist darauf ausgelegt, die verschiedenen Komponenten einer Applikation in einzelnen Containern auszuführen. Eine Kommunikation zwischen den Containern ist einfach möglich.

### SSL-Zertifikate

Lösung: [https://github.com/jwilder/nginx-proxy](https://github.com/jwilder/nginx-proxy)

### schnelle Einrichtung

Die Einrichtung eines neuen Projektes beinhaltet folgende Schritte:

1. **docker-compose Datei** ins Projekt kopieren und Konfigurationen vornehmen
2. dev url in **hosts Datei** eintragen: `sudo nano /etc/hosts` eintragen
3. **SSL Zertifikat** für lokale URL erstellen `sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout dev.developer-camp.de.key -out dev.developer-camp.de.crt`
4. Alle **Container hochfahren** mit Hilfe von `docker-compose up -d`
5. Falls **Datenbank** nötig ist: Datenbank entweder über PhpMyAdmin oder über die Konsole anlegen

### Performance

Na ja ...

Auf System-Seite kein Problem. Rechner-Ressourcen reichen ohne Probleme aus um verschiedenste Docker-Container parallel aktiv zu haben.

Das Problem ist hier die Einbindung der Volumes. Die Syncronisation der Daten in die Container dauert zu lange. Hier wird aber gerade viel getan. Hoffnung: Bald wird es besser ...

### dev = stage = live

Ist möglich, aber zur Zeit noch nicht umgesetzt.

## Bewertung / erreichte Ziele

- [x] Microservice Architektur
- [x] einfaches "hochfahren"
- [x] vhosts
- [x] SSL-Zertifikate
- [x] schnelle Einrichtung
- [-] Performance
- [ ] dev = stage = live

## Links

### Docker Images

Vorsicht: nicht einfach für Live-Systeme verwenden!

#### Nginx-Proxy mit SSL Unterstürtzung

- [https://gitlab.com/rofthedeep/docker-proxy-ssl](https://gitlab.com/rofthedeep/docker-proxy-ssl)

#### PHP

- [https://gitlab.com/rofthedeep/docker-php](https://gitlab.com/rofthedeep/docker-php)

#### Shopware & Magento

- [https://gitlab.com/rofthedeep/docker-shopware](https://gitlab.com/rofthedeep/docker-shopware) 

### Docker Performance

- [http://markshust.com/2017/03/02/making-docker-mac-faster-overlay2-filesystem](http://markshust.com/2017/03/02/making-docker-mac-faster-overlay2-filesystem)
- [https://docs.docker.com/docker-for-mac/osxfs-caching/#examples](https://docs.docker.com/docker-for-mac/osxfs-caching/#examples)
- [https://gist.github.com/yallop/d4af9dd1bb33ae61d48adf86692cdf9e](https://gist.github.com/yallop/d4af9dd1bb33ae61d48adf86692cdf9e)
