# Guida Configurazione

Guida completa per configurare e utilizzare il Meshtastic Node Monitor.

---

## Indice

1. [Configurazione Nodo Meshtastic](#configurazione-nodo-meshtastic)
2. [Configurazione Versione Base](#configurazione-versione-base)
3. [Configurazione Versione Avanzata](#configurazione-versione-avanzata)
4. [Risoluzione Problemi](#risoluzione-problemi)

---

## Configurazione Nodo Meshtastic

### Prerequisiti

- Nodo Meshtastic con firmware 2.0+
- Connessione WiFi alla stessa rete del tuo computer

### Passo 1: Abilitare WiFi

**Via App Meshtastic (Mobile/Desktop):**
1. Apri l'app Meshtastic
2. Connettiti al tuo nodo via Bluetooth
3. Vai in **Settings → Network → WiFi**
4. Abilita WiFi e inserisci:
   - SSID: nome della tua rete WiFi
   - Password: password della rete
5. Salva e riavvia il nodo

**Via CLI:**
```bash
# Installa meshtastic CLI
pip install meshtastic

# Configura WiFi
meshtastic --set network.wifi_enabled true
meshtastic --set network.wifi_ssid "NOME_RETE"
meshtastic --set network.wifi_psk "PASSWORD"

# Verifica configurazione
meshtastic --info
```

### Passo 2: Verificare IP del Nodo

Dopo il riavvio, il nodo mostrerà il suo IP:

**Via Display OLED (se presente):**
- L'IP appare nella schermata principale

**Via App:**
- Settings → Device → IP Address

**Via CLI:**
```bash
meshtastic --info | grep -i ip
```

### Passo 3: Testare API

Apri il browser e naviga a:
```
http://{IP_NODO}/json/report
```

Dovresti vedere un JSON con i dati del nodo.

---

## Configurazione Versione Base

La versione base (`monitor-fixed-ip.html`) utilizza un campo input per connettersi dinamicamente al nodo. Non è necessario modificare il codice sorgente.

### Connessione al Nodo

1. Apri `monitor-fixed-ip.html` nel browser
2. Inserisci l'IP del tuo nodo Meshtastic nel campo **"Indirizzo IP"** (es. `192.168.2.191`)
3. Imposta l'intervallo di aggiornamento (default: 10 secondi, minimo: 5 secondi)
4. Clicca **"Connetti"**

L'indirizzo IP e l'intervallo di refresh vengono salvati automaticamente nel browser (localStorage), quindi al prossimo accesso saranno già precompilati.

### Cambiare Nodo o Intervallo

Per utilizzare un diverso nodo o modificare la frequenza di aggiornamento:
- Modifica i valori nei campi input
- Clicca nuovamente **"Connetti"**
- I nuovi valori sovrascrivono automaticamente quelli salvati

---

## Configurazione Versione Avanzata

La versione avanzata (`monitor-network-discovery.html`) permette configurazione dinamica.

### Interfaccia Utente

```
┌─────────────────────────────────────────────────────────┐
│  IP: [192.168.2.191  ]  Refresh: [10] sec  [Connetti]  │
│                                         [Trova Nodi]   │
│                              [● Offline]                 │
└─────────────────────────────────────────────────────────┘
```

**Campi:**
- **IP**: Indirizzo del nodo Meshtastic
- **Refresh**: Intervallo aggiornamento in secondi (minimo 10)
- **Connetti**: Avvia monitoraggio continuo
- **Trova Nodi**: Apre pannello network discovery

### Network Discovery

Cliccando "Trova Nodi" si apre il pannello scanner:

#### Test Rapido Singolo IP

Per verificare se un nodo risponde:
1. Inserisci l'IP nel campo "Test Rapido"
2. Clicca **Test IP**
3. Controlla la console log per il risultato

#### Scansione Range Personalizzato

**Formato CIDR** (tutta la subnet):
```
192.168.2.0/24    → scansiona 192.168.2.1 - 192.168.2.254
192.168.2.0/23    → scansiona 2 subnet (510 host)
```

**Formato Range**:
```
192.168.2.1-50    → scansiona 192.168.2.1 - 192.168.2.50
192.168.2.1-192.168.2.100  → range completo
```

**Formato Lista**:
```
192.168.2.1,192.168.2.10,192.168.2.191
```

#### Scansione Automatica

Rileva automaticamente la subnet del tuo computer e scansiona i range comuni:
- `192.168.x.0/24`
- `10.0.x.0/24`
- `172.16.x.0/24`

### Persistenza Dati

L'IP e l'intervallo di refresh vengono salvati automaticamente in `localStorage`.

**Per cancellare i dati salvati:**

Apri la console del browser (F12) e digita:
```javascript
localStorage.removeItem('meshtastic_ip');
localStorage.removeItem('meshtastic_refresh_interval');
```

---

## Risoluzione Problemi

### Problema: "Offline" nonostante IP corretto

**Cause possibili:**

1. **CORS Blocked**
   - **Sintomo**: Errore in console: `CORS policy`
   - **Soluzione**: Apri il file via HTTP, non HTTPS
   ```bash
   # Avvia server locale
   python -m http.server 8080
   # Poi vai a http://localhost:8080
   ```

2. **Nodo non raggiungibile**
   - **Test**: `ping {IP_NODO}`
   - **Test API**: `curl http://{IP_NODO}/json/report`

3. **Web Server non abilitato**
   - **Verifica**: Nelle impostazioni del nodo, controlla che il web server sia ON

### Problema: Scansione trova 0 nodi

**Cause possibili:**

1. **Range sbagliato**
   - Verifica la subnet corretta con `ipconfig` (Windows) o `ifconfig` (Linux/Mac)

2. **Timeout troppo breve**
   - La scansione usa timeout di 15 secondi per IP
   - Reti grandi richiedono più tempo

3. **Firewall**
   - Verifica che non ci siano firewall bloccare le richieste HTTP

### Problema: Dati non aggiornati

**Versione base:**
- I dati sono statici, devi aggiornare manualmente il JSON

**Versione avanzata:**
- Verifica che "Refresh" sia impostato correttamente
- Controlla che il nodo sia "Online"
- Verifica nella console del browser eventuali errori

---

## Configurazioni Avanzate

### Cambiare Porta Web Server (Meshtastic)

Default: porta 80

```bash
meshtastic --set network.webserver_port 8080
```

Poi accedi a: `http://{IP}:8080/json/report`

### Autenticazione (se abilitata)

Se hai abilitato l'autenticazione sul web server:

```javascript
// Nella funzione connect(), aggiungi header Authorization
const response = await fetch(`http://${ip}/json/report`, {
    headers: {
        'Authorization': 'Basic ' + btoa('username:password')
    }
});
```

---

## Supporto

Per problemi o domande:
- [Meshtastic Forum](https://meshtastic.discourse.group/)
- [Meshtastic Discord](https://discord.gg/meshtastic)
- GitHub Issues (questo repository)
