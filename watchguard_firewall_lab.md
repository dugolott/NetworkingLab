# 🧪 Laboratorio Firewall WatchGuard

## 🎯 Obiettivi

- Comprendere il funzionamento di un firewall stateful (WatchGuard)
- Configurare interfacce WAN/LAN e servizi DHCP
- Definire e testare policy di sicurezza (ACL)
- Analizzare traffico e log
- Implementare NAT e VPN
- Sperimentare scenari reali di attacco/difesa

---

## 🧠 Competenze

- configurazione firewall
- networking IP (DHCP, NAT, routing)
- analisi traffico (log, monitoring)
- sicurezza (ACL, segmentazione)
- troubleshooting
- lavoro collaborativo

---

## 👥 Organizzazione (gruppi da 4–5)

- **Coppia 1** → Configurazione base + DHCP
- **Coppia 2** → Policy / ACL / NAT
- **Coppia 3** → Monitoraggio / Logging / Analisi traffico
- **+1** → Documentazione e coordinamento

---

## 🧭 Fasi del laboratorio

### 1️⃣ Setup iniziale

- Porta0 → WAN (DHCP client)
- Porta1 → LAN (DHCP attivo)

Accesso:
- utente: `status` / `readonly`
- admin: `readwrite` (per salvare)

✔ Verifica:
- assegnazione IP ai client
- connettività interna

---

### 2️⃣ Configurazione rete LAN

- Riconfigurazione pool DHCP:
  - dimensionato sull'isola
  - mantenere coerenza con rete originale
- Verifica NIC dei laptop

✔ Test:
- ogni host riceve IP corretto
- ping tra host LAN

---

### 3️⃣ Configurazione NAT

- SNAT per uscita verso WAN
- Abilitazione ping su interfaccia WAN

✔ Test:
- ping verso esterno (prof)
- navigazione internet

---

### 4️⃣ Definizione Policy (ACL)

#### Policy 1
- blocco ICMP da host specifico verso esterno

#### Policy 2
- blocco traffico web (HTTP/HTTPS) da altro host

✔ Verifica:
- comportamento differenziato tra host
- test con browser e ping

---

### 5️⃣ Logging e Monitoraggio

Attivare:
- logging delle policy
- monitoraggio traffico
- (opzionale) port mirroring

✔ Generare eventi:
- traffico web
- ping bloccati
- richieste DHCP

✔ Analisi:
- verificare log ACL
- identificare host e violazioni

---

### 6️⃣ VPN Client-to-Site

- configurazione VPN client (docente)
- accesso remoto alla LAN

✔ Test:
- connessione VPN attiva
- accesso a host interni

---

### 7️⃣ VPN Site-to-Site

- collegamento tra 2 o più isole

✔ Test:
- ping tra reti diverse
- verifica routing

---

## 🔍 Attività di Analisi

- correlare traffico ↔ log firewall
- distinguere traffico permesso/bloccato
- analizzare comportamento NAT

---

## 🚀 Attività aggiuntive (consigliate)

### 🔸 1. Simulazione attacco interno
- scansione porte (nmap)
- verifica rilevamento nei log

### 🔸 2. Policy “default deny”
- bloccare tutto
- aprire solo servizi necessari

### 🔸 3. Controllo per applicazione
- bloccare social / streaming
- verificare comportamento

### 🔸 4. Confronto prestazioni
- con/ senza logging attivo
- latenza percepita

### 🔸 5. Troubleshooting guidato
Docente introduce errore:
- NAT errato
- DHCP mal configurato
- policy incoerente

Studenti devono:
- diagnosticare
- risolvere

---

## 📄 Output richiesto

### 🎥 Video (5–10 min)

1. Setup
2. Configurazione base
3. Policy
4. Test
5. Analisi log
6. VPN

---

### 📘 README.md

```
# WatchGuard Lab

## Setup
## Configurazione rete
## Policy
## NAT
## VPN
## Analisi log
## Problemi riscontrati
## Soluzioni
```

---

## 📊 Valutazione

| Criterio | Peso |
|--------|------|
| Configurazione rete | 20% |
| Policy / ACL | 25% |
| NAT / VPN | 20% |
| Analisi log | 20% |
| Documentazione | 15% |

---

## ⚠️ Criticità

- coerenza indirizzamento IP
- ordine delle policy
- NAT vs ACL (ordine di applicazione)
- logging troppo verboso

---

## 💡 Domande guida

- In che ordine vengono applicate NAT e ACL?
- Perché una policy non funziona anche se “corretta”?
- Come distinguere traffico bloccato da traffico non instradato?
- Che differenza c’è tra filtraggio per IP e per servizio?
- Quali vantaggi offre una VPN rispetto all’accesso diretto?

