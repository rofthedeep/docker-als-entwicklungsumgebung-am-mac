# Docker als Entwicklungsumgebung am Mac

## Problem und Ziel

Mamp / Lamp Entwicklugnsumgebung war nicht mehr flexibel genug. Gerade bei der Entwicklung mit verschiedenen PHP Versionen kam es immer wieder zu Problemen

**Ziel:** flexible lokale Entwicklungsumgebung, die je nach Projekt dessen technische Anforderungen erfüllt

## Anforderungen

- **Microservice Architektur** soll möglich sein 
- **einfaches "hochfahren"**: Entwicklungsumgebungen sollten schnell verfügbar sein
- **vhosts** "dev.website.de" muss auch weiterhin funktionieren (Lizenzen etc.)
- **SSL-Zertifikate** müssen funktionieren. Z.B. für Entwicklung und Tests von Payment Schnittstellen
- **schnelle Einrichtung**: die Einrichtung neuer Projekte muss schnell gehen
- **Performance**: Ladezeiten müssen im Rahmen bleiben
- **dev = stage = live**: Die Entwicklungsumgebung soll mit der gleichen Struktur laufen, wie auch die Liveumgebung

## Umsetzung

### Microservice Architektur

**Docker** ist darauf ausgelegt, die verschiedenen Komponenten einer Applikation in einzelnen Containern auszuführen. Eine Kommunikation zwischen den Containern ist einfach möglich.

### einfaches hochfahren

**Docker Compose** ist eine Konfigurationsdatei für Docker Container. Über die Kommandozeile können so alle Container mit Hilfe eines Befehls `docker-compose up -d` gestartet werden.

### vhosts

Lösung: [https://github.com/jwilder/nginx-proxy](https://github.com/jwilder/nginx-proxy)

- Proxy, der als Container gestartet wird (Auch einfach über `docker-compose up -d`)
- dieser leitet Einträge aus der Hosts Datei an entsprechende Container weiter
- die dev URL muss in der docker-compose Datei des Projektes eingetragen werden

### schnelle Einrichtung

Die Einrichtung eines neuen Projektes beinhaltet folgende Schritte:

1. **docker-compose Datei** ins Projekt kopieren und Konfigurationen vornehmen
2. dev url in **hosts Datei** eintragen: `sudo nano /etc/hosts` eintragen
3. **SSL Zertifikat** für lokale URL erstellen `sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout dev.url.de.key -out dev.url.de.crt`
4. Alle **Container hochfahren** mit Hilfe von `docker-compose up -d`
5. Falls **Datenbank** nötig ist: Datenbank entweder über PhpMyAdmin oder über die Konsole anlegen

### einfaches hochfahren

Per Konsole in das Verzeichis wechseln, dann: `docker-compose up -d`

### SSL-Zertifikate

Lösung: [https://github.com/jwilder/nginx-proxy](https://github.com/jwilder/nginx-proxy)

Hier müssen drei Elemente zusammen spielen:

- der proxy muss das Zertifikats-Verzeichnis kennen
- der Applikations-Container muss das Zertifikats-Verzeichnis kennen
- die Apache Configs des Applikations-Containers müssen die Verzeichnisse kennen

#### Wie funktioniert das Einbinden der SSL Zertifikate

Dafür muss es im System einen Ordner mit den Zertifikaten geben. Auf diesen Ordner müssen sowohl der Proxy als auch die "Applikation" zugreifen. Hierfür muss der Ordner in beiden als Volume eingebunden werden.

### Performance

Na ja ...

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

### Docker Performance in der Zukunft

- [http://markshust.com/2017/03/02/making-docker-mac-faster-overlay2-filesystem](http://markshust.com/2017/03/02/making-docker-mac-faster-overlay2-filesystem)
- [https://docs.docker.com/docker-for-mac/osxfs-caching/#examples](https://docs.docker.com/docker-for-mac/osxfs-caching/#examples)
- [https://gist.github.com/yallop/d4af9dd1bb33ae61d48adf86692cdf9e](https://gist.github.com/yallop/d4af9dd1bb33ae61d48adf86692cdf9e)
