# 🧪 DHCP Starvation & Contromisure – Laboratorio di Reti

Proposta di laboratorio pratico per comprendere e contrastare l'attacco di DHCP Starvation, che mira a esaurire le risorse del server DHCP, impedendo ai client legittimi di ottenere un indirizzo IP.

## Competenze

- script di networking con Scapy
- configurazione di server DHCP su Linux
- configurazione router/firewall/switch: DHCP, snooping, port security
- analisi traffico con Wireshark e log server DHCP
- progettazione e verifica contromisure
- documentazione tecnica e comunicazione
- lavoro di gruppo e coordinamento

---

## 🎯 Obiettivi

- comprendere vulnerabilità DHCP
- implementare attacco controllato
- progettare contromisure
- documentare in modo tecnico

---

## 👥 Organizzazione gruppi (4–5 studenti)

### Ruoli

#### 🟥 Coppia 1 — Attaccanti

- script Scapy DHCP starvation
- parametrizzazione (rate, MAC random)
- verifica traffico (Wireshark)

👉 focus: scripting python / protocollo DHCP

---

#### 🟦 Coppia 2 — Server DHCP (Linux VM)

- installazione (`isc-dhcp-server` o `dnsmasq`)
- configurazione pool piccolo (es. /27)
- log DHCP attivi

👉 focus: SO / server DHCP sw / Logging

---

#### 🟩 Coppia 3 — Rete & Difese (router/switch)

- DHCP server su router (o relay)
- configurazione:
  - DHCP snooping
  - port security
  - logging
- monitoraggio eventi

👉 focus: Firewall / Security / Monitoring

---

#### 🟨 (eventuale 5° studente)

- coordinamento + video + documentazione

---

## 🧭 Fasi del lavoro

### 1️⃣ Setup (baseline)

- configurazione rete (isole indipendenti)
- DHCP funzionante  --> connessione altri client dell'isola o VM
- test con Wireshark (`bootp`)

---

### 2️⃣ Attacco

- esecuzione script starvation
- osservazioni:
  - lease saturati
  - nuovi client senza IP --> rete KO

---

### 3️⃣ Analisi

- traffico Wireshark
- log DHCP server
- comportamento rete

---

### 4️⃣ Difesa

- sostituzione con router/firewall come DHCP server
- attivazione protezioni:
  1. DHCP snooping
  2. port security
  3. rate limiting

---

### 5️⃣ Verifica

- rilancio attacco
- confronto:
  - prima → rete KO
  - dopo → rete stabile
- analisi differenze traffico/log

---

### Extra: confronto DHCP Linux vs router

- parametri di configurazione
- misurare tempo saturazione
- differenze log/monitoraggio

---

## Output richiesto

- video dimostrativo (5–10 min)
- documentazione tecnica (README.md)
Durata: 5–5 minuti max

### Struttura obbligatoria VIdeo

1. Setup (topologia, ruoli)
2. Baseline (DHCP OK)
3. Attacco (script + effetto)
4. Analisi (Wireshark / log)
5. Contromisure
6. Verifica

---

### 📄 README.md

```md

# DHCP Starvation Lab

## Setup
## Attacco
## Evidenze (log / capture)
## Difese
## Risultati

```

---

## 📊 Criteri di Valutazione

| Criterio | Peso |
|--------|------|
| Funzionamento attacco | 25% |
| Configurazione DHCP | 20% |
| Contromisure | 25% |
| Analisi (log/Wireshark) | 15% |
| Video/documentazione | 15% |

---

## ⚠️ Criticità

- isolare ogni gruppo (VLAN/isola)
- pool piccolo → effetto rapido
- sincronizzazione tra coppie

---

### 🔥 Domande guida

- perché DHCP è vulnerabile?
- perché le difese stanno nello switch?
- perché MAC spoofing funziona?
- ...

---

