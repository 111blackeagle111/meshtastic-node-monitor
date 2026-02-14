# Changelog

Tutte le modifiche significative a questo progetto saranno documentate in questo file.

Il formato è basato su [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
e questo progetto aderisce a [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2025-02-14

### Aggiunto
- Versione base `monitor-fixed-ip.html` con IP hardcoded
- Versione avanzata `monitor-network-discovery.html` con:
  - Input IP configurabile
  - Network discovery con scanner
  - Test singolo IP con logging
  - Supporto range CIDR, range IP, lista IP
  - Refresh automatico configurabile
  - Persistenza dati in localStorage
- Tema dark responsive
- 6 card dashboard: Alimentazione, Connettività, Radio LoRa, Memoria, Sistema, Traffico
- Documentazione completa (README, CONFIGURATION)
- Licenza GPL v3 (copyleft - protegge il codice open source)

### Note
- Compatibile con API Meshtastic `/json/report`
- Nessuna dipendenza esterna richiesta
- Puro HTML/CSS/JavaScript
