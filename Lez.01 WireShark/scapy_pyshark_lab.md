# Networking  da Wireshark allo scripting: Scapy vs PyShark

---

## Introduzione a Scapy

Scapy è una libreria Python per:

- costruire pacchetti
- inviarli
- analizzarli

Lavora a **basso livello (layer)**.

---

### Esempi Scapy

#### Inviare un ping

```python
from scapy.all import *

pkt = IP(dst="8.8.8.8")/ICMP()
send(pkt)
```

#### Leggere un PCAP

```python
from scapy.all import rdpcap

pkts = rdpcap("capture.pcap")

for p in pkts:
    print(p.summary())
```

#### Verifica layer DNS

```python
from scapy.all import DNS

if p.haslayer(DNS):
    print("Pacchetto DNS")
```

---

## Introduzione a PyShark

PyShark usa tshark (Wireshark CLI).
tshark (e quindi Wireshark) legge (sniffa) i pacchetti a livello di driver di rete: i dati vengono forniti già interpretati/decodificati, dalla libreria winpcap/libpcap, che intercetta i pacchetti raw dalla scheda di rete.

Lavora ad **alto livello (campi già interpretati)**.

### Winpcap/libpcap

Più che una libreria, è un driver di rete che permette di leggere i pacchetti a livello di driver, prima che vengano processati dallo stack di rete del sistema operativo. Fornisce wrapper per accedere a questa funzionalità da con numerosi linguaggi di programmazione, tra cui Python (usato da PyShark) e C (usato da tshark).

Infatti, `winpcap/libpcap` si installano a livello di kernel, operano a livello 2 e intercettando lo stream di paccketti prima che il NIC li passi allo stack di rete del sistema operativo. Per tale motivo, l'installazione di winpcap/libpcap richiede privilegi di amministratore; una volta installati, possono essere usati da qualsiasi programma in usermode (come tshark) per leggere i pacchetti a livello di driver, senza bisogno di ulteriori privilegi.

Altra applicazione spesso usata per leggere i pacchetti a livello di driver è `tcpdump`: comando CLI che, permette di esportare i pacchetti in un file pcap, che poi può essere letto da tshark, Wireshark o PyShark.

Da sapere su winpcap/libpcap:

- può lavorare in moidalità ***promiscua*** (la scheda di rete intercetta tutti i pacchetti che passano sulla rete, non solo quelli destinati al proprio indirizzo MAC)
- i ***filtri di cattura*** girano nel kernel (BPF - Berkeley Packet Filter) e permettono di ridurre il numero di pacchetti che vengono passati all'applicazione (tshark), migliorando le prestazioni.

### Esempi PyShark

#### Leggere un PCAP

```python
import pyshark

cap = pyshark.FileCapture("capture.pcap")

for pkt in cap:.
    print(pkt)
```

Fornisce una rappresentazione con campi già interpretati in stile Wireshark.

### Solo DNS

```python
cap = pyshark.FileCapture("capture.pcap", display_filter="dns")

for pkt in cap:
    print(pkt.dns.qry_name)
```

---

## 📊 Scapy vs PyShark (DNS / PCAP)

| Idea | Scapy | PyShark | Nota|
|---------|------|--------|----------|
| Aprire PCAP | `rdpcap("file.pcap")` | `pyshark.FileCapture("file.pcap")` | Scapy carica tutto in memoria, PyShark streamma via tshark |
| Iterare pacchetti | `for p in pkts:` | `for pkt in cap:` | PyShark è uno stream, non una lista |
| Verifica IP | `p.haslayer(IP)` | `'IP' in pkt` | PyShark espone i protocolli come layer testuali |
| Solo query DNS | `p[DNS].qr == 0` | `pkt.dns.flags_response == '0'` | `qr=0` → query, `1` → response (in PyShark è stringa) |
| Nome query | `p[DNSQR].qname` | `pkt.dns.qry_name` | Scapy separa DNS/DNSQR struttura interna|
| Tipo query | `p[DNSQR].qtype` | `pkt.dns.qry_type` | In Scapy è numerico, PyShark solitamente stringa |
| Layer presenti | `p.show()` / `payload` | `pkt.layers` | Scapy naviga a oggetti, PyShark lista layer |
| Filtro | Python (`if ...`) | `display_filter="dns"` | PyShark usa i filtri Wireshark (molto potente) |
| Indirizzo IP sorgente | `p[IP].src` | `pkt.ip.src` | Con Scapy accesso diretto ai protocolli |
| Porta TCP | `p[TCP].dport` | `pkt.tcp.dstport` | ...ogni layer è un elemento del dizionario |
| MAC sorgente | `p[Ether].src` | `pkt.eth.src` | Con PyShark accesso ai layer membri di un oggetto |
| Verifica HTTP | `p.haslayer(Raw)` (manuale) | `'HTTP' in pkt` | Scapy non ha dissector per tutti i protocolli |
| ARP | `p.haslayer(ARP)` | `'ARP' in pkt` | |
| ICMP | `p.haslayer(ICMP)` | `'ICMP' in pkt` | Ping / errori |
| Payload raw | `p[Raw].load` | `pkt.data.data` | `Raw` come `.data` rappresentano il primo protocollo non riconosciuto, l'attributo finale `.load` o `.data` restituisce il payload |
| Lunghezza pacchetto | `len(p)` | `pkt.length` | Alcuni campi di PyShark sono astrazioni dei campi originali |

---

## Scapy vs PyShark: semplice script per i pacchetti di un PCAP

### Scapy

```python
from scapy.all import rdpcap

pkts = rdpcap("capture.pcap") #scapy ritorna una lista, possiamo iterare direttamente

for p in pkts[:5]:
    layer = p
    print("\nPacchetto:")
    while layer:
        print(layer.name, end=" -> ")
        layer = layer.payload
    print("END")
```

---

### PyShark

```python
import pyshark

cap = pyshark.FileCapture("capture.pcap") #PyShark ritorna uno stream... meno opzioni per iterare, ma più efficiente: non carica tutto in memoria, ma legge via tshark

for pkt in cap:
    print("\nPacchetto:")
    for layer in pkt.layers:
        print(layer.layer_name, end=" -> ")
    print("END")
```