# Meshtastic Node Monitor

Dashboard web per il monitoraggio in tempo reale dei nodi **Meshtastic** via HTTP API. Progetto in puro HTML/CSS/JavaScript - nessuna dipendenza esterna richiesta.

![Version](https://img.shields.io/badge/version-1.0.0-blue)
![License](https://img.shields.io/badge/license-GPL%20v3-red)
![Meshtastic](https://img.shields.io/badge/meshtastic-compatible-orange)

---

## 📁 Struttura del Progetto

```
meshtastic-node-monitor/
├── examples/
│   ├── monitor-fixed-ip.html          # Versione base - IP fisso
│   └── monitor-network-discovery.html # Versione avanzata - Network scanner
├── docs/
│   └── CONFIGURATION.md               # Guida configurazione
├── README.md                          # Questo file
└── .gitignore
```

---

## 🚀 Due Versioni a Confronto

### 1️⃣ Versione Base: `monitor-fixed-ip.html`

> **Caso d'uso**: Monitoraggio di un singolo nodo con IP noto e fisso

**Caratteristiche:**
- ✅ IP del nodo **hardcoded** nel codice (`192.168.2.191`)
- ✅ Dati **statici** (mock JSON incluso)
- ✅ Visualizzazione **read-only**
- ✅ Design responsive con tema dark
- ✅ Nessuna configurazione richiesta

**Quando usarla:**
- Hai un solo nodo da monitorare
- L'IP del nodo non cambia mai
- Vuoi una dashboard semplice e immediata
- Non hai bisogno di aggiornamenti in tempo reale

```javascript
// IP configurato direttamente nel codice
const data = {
    "wifi": {
        "ip": "192.168.2.191",  // ← Modifica qui
        "rssi": -71
    },
    // ... altri dati
};
```

---

### 2️⃣ Versione Avanzata: `monitor-network-discovery.html`

> **Caso d'uso**: Monitoraggio dinamico con ricerca automatica nodi nella rete

**Caratteristiche:**
- ✅ **Input IP configurabile** dall'utente
- ✅ **Network Discovery** - Scansione automatica della rete
- ✅ **Test singolo IP** con logging dettagliato
- ✅ **Range scanner** (CIDR, range IP, lista)
- ✅ **Refresh automatico** configurabile (min 10 secondi)
- ✅ **Persistenza** IP in localStorage
- ✅ **Console log** per debug
- ✅ Stato connessione in tempo reale

**Quando usarla:**
- Hai più nodi da monitorare
- Gli IP dei nodi possono cambiare
- Vuoi scoprire automaticamente i nodi nella rete
- Hai bisogno di aggiornamenti periodici
- Vuoi testare la connettività prima di connetterti

**Funzionalità di scansione:**

| Metodo | Formato | Esempio |
|--------|---------|---------|
| CIDR | `xxx.xxx.xxx.xxx/xx` | `192.168.2.0/24` |
| Range | `xxx.xxx.xxx.xxx-yyy` | `192.168.2.1-50` |
| Lista | `ip1,ip2,ip3` | `192.168.2.1,192.168.2.10` |

---

## 📊 Dashboard - Metriche Visualizzate

Entrambe le versioni mostrano le stesse metriche:

| Card | Metriche |
|------|----------|
| 🔋 **Alimentazione** | Percentuale batteria, voltaggio, stato carica |
| 📶 **Connettività** | Indirizzo IP, RSSI WiFi, indicatori segnale |
| 📡 **Radio LoRa** | Frequenza, canale, utilizzo canale |
| 💾 **Memoria** | Heap usato/libero, Filesystem usato/libero |
| ⚙️ **Sistema** | Uptime, contatore reboot, stato sistema |
| 📊 **Traffico** | RX/TX ultimi periodi, storico 8 periodi |

---

## 🔧 Requisiti

- Nodo Meshtastic con **Web Server abilitato**
- Accesso alla stessa rete del nodo
- Browser moderno (Chrome, Firefox, Edge, Safari)

### Abilitare Web Server su Meshtastic

```bash
# Via CLI
meshtastic --set network.wifi_enabled true
meshtastic --set network.wifi_ssid "TUO_SSID"
meshtastic --set network.wifi_psk "TUA_PASSWORD"

# Oppure via interfaccia del nodo
# Settings → Network → WiFi → Enable
```

---

## 🌐 API Endpoint Utilizzato

```
GET http://{IP_NODO}/json/report
```

**Risposta JSON:**
```json
{
  "status": "ok",
  "data": {
    "airtime": { ... },
    "device": { ... },
    "memory": { ... },
    "power": { ... },
    "radio": { ... },
    "wifi": { ... }
  }
}
```

---

## ⚠️ Note Importanti

### CORS (Cross-Origin Resource Sharing)

Se accedi alla dashboard via **HTTPS**, il browser bloccherà le richieste HTTP al nodo.

**Soluzioni:**
1. Apri il file HTML direttamente con **HTTP** (non HTTPS)
2. Usa un server locale: `python -m http.server 8080`
3. Per testing: avvia Chrome con `--disable-web-security` (non consigliato in produzione)

### Timeout

La versione avanzata usa un timeout di **15 secondi** per le richieste di scansione.

---

## 🎨 Personalizzazione

### Cambiare il colore del tema

Modifica le variabili CSS in `:root`:

```css
:root {
    --accent: #00d4ff;        /* Colore principale */
    --success: #00ff88;       /* Colore successo */
    --warning: #ffaa00;       /* Colore warning */
    --danger: #ff4444;        /* Colore errore */
}
```

### Cambiare IP default (versione avanzata)

```javascript
// Riga 671 nel file HTML
<input type="text" id="ip-input" value="192.168.2.191">
```

---

## 📱 Screenshot

### Versione Base
```
┌─────────────────────────────────────┐
│      Meshtastic Node Monitor        │
│        Monitoraggio statico         │
├──────────┬──────────┬───────────────┤
│ 🔋 100%  │ 📶 -71   │ 📡 869.5 MHz  │
│ Batteria │ dBm      │ LoRa Ch 1     │
├──────────┴──────────┴───────────────┤
│ 💾 Memoria  │  ⚙️ Sistema          │
│ Heap: 92%   │  Uptime: 02:25:07    │
│ FS: 3%      │  Reboot: 6,184       │
└─────────────────────────────────────┘
```

### Versione Avanzata
```
┌────────────────────────────────────────────────────┐
│ IP: [192.168.2.191]  Refresh: [10]  [Connetti]    │
│                              [Trova Nodi] [Online] │
├────────────────────────────────────────────────────┤
│ ┌──────────────────────────────────────────────┐  │
│ │  Network Scanner                              │  │
│ │  [Test IP] [Scansiona Range] [Auto Scan]     │  │
│ │  ▓▓▓▓▓▓▓░░░░░░░░░░░░░░░░░░░░░░░░  25%        │  │
│ │  • 192.168.2.191  ✓ Online  -71 dBm          │  │
│ │  • 192.168.2.192  ✓ Online  -68 dBm          │  │
│ └──────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────┘
```

---

## 🤝 Contribuire

1. Fork del repository
2. Crea un branch: `git checkout -b feature/nuova-funzione`
3. Committa le modifiche: `git commit -am 'Aggiunta nuova funzione'`
4. Push al branch: `git push origin feature/nuova-funzione`
5. Apri una Pull Request

---

## 📄 Licenza

**GNU General Public License v3.0 (GPL-3.0)**

Questo progetto è software libero: puoi ridistribuirlo e/o modificarlo secondo i termini della GNU General Public License come pubblicata dalla Free Software Foundation, versione 3 della Licenza, o (a tua scelta) qualsiasi versione successiva.

**Cosa significa GPL v3 per te:**
- ✅ Puoi usare, modificare e distribuire liberamente questo codice
- ✅ Se modifichi e distribuisci, DEVI rilasciare anche il tuo codice sotto GPL v3
- ✅ Non puoi incorporare questo codice in software proprietario closed-source
- ✅ Chiunque riceva il software ha diritto al codice sorgente

Vedi il file [LICENSE](LICENSE) per il testo completo.

**Perché GPL v3?** Per proteggere questo progetto e assicurare che rimanga sempre open source, contribuendo alla community senza che venga sfruttato in progetti proprietari.

---

## 🔗 Link Utili

- [Meshtastic Official](https://meshtastic.org/)
- [Meshtastic GitHub](https://github.com/meshtastic)
- [Meshtastic Discord](https://discord.gg/meshtastic)

---

**Creato con ❤️ per la community Meshtastic**
