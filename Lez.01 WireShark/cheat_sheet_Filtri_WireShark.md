# Cheat Sheet — Filtri Wireshark

## 1) Operatori principali

| Operatore         | Significato                                  | Esempio                                       | Note                                                            |
| ----------------- | -------------------------------------------- | --------------------------------------------- | --------------------------------------------------------------- |
| `==`              | uguaglianza                                  | `ip.addr == 192.168.1.10`                     | Operatore più usato nei display filter.                         |
| `!=`              | diverso da                                   | `tcp.port != 80`                              | Utile per escludere rapidamente un valore.                      |
| `>` `<` `>=` `<=` | confronto numerico                           | `frame.len > 1500`                            | Funziona bene su lunghezze, numeri di porta, TTL, codici, ecc.  |
| `contains`        | contiene una sottostringa o sequenza di byte | `http.host contains "google"`                 | Molto utile per testo applicativo; attenzione: non è una regex. |
| `matches`         | confronto con espressione regolare           | `http.request.uri matches "login\|admin"`     | Potente ma più costoso; usa sintassi regex.                     |
| `in { ... }`      | appartenenza a un insieme                    | `tcp.port in {80 443 8080}`                   | Più leggibile di molte OR concatenate.                          |
| `&&`              | AND logico                                   | `ip && tcp`                                   | Entrambe le condizioni devono essere vere.                      |
| `\|\|`            | OR logico                                    | `dns \|\| icmp`                               | Almeno una condizione vera.                                     |
| `!`               | negazione                                    | `!(arp \|\| dns)`                             | Utile per escludere protocolli o casi rumorosi.                 |
| `()`              | raggruppamento logico                        | `(tcp.port == 80 \|\| tcp.port == 443) && ip` | Fondamentale per evitare ambiguità nelle precedenze.            |
| `bitwise and`     | maschera bit a bit                           | `tcp.flags & 0x02`                            | Usato soprattutto sui flag TCP o campi binari.                  |
| `:`               | slice / accesso a porzione di byte           | `data.data[0:4]`                              | Più usato in filtri avanzati su payload grezzo.                 |

## 2) Operandi / campi molto usati

| Operando / campo                                                     | Breve spiegazione                                                    | Usato spesso per...                                                                |
| -------------------------------------------------------------------- | -------------------------------------------------------------------- | ---------------------------------------------------------------------------------- |
| `ip`, `ipv6`                                                         | Filtra genericamente traffico IPv4 o IPv6.                           | Separare velocemente il traffico di livello rete.                                  |
| `ip.addr`, `ipv6.addr`                                               | Indirizzo IP coinvolto nel pacchetto, sia sorgente sia destinazione. | Trovare tutto il traffico relativo a un host.                                      |
| `ip.src`, `ip.dst`                                                   | IP sorgente / destinazione.                                          | Analizzare direzione del traffico, client vs server, attacco vs bersaglio.         |
| `tcp`, `udp`                                                         | Filtra genericamente pacchetti TCP o UDP.                            | Distinguere traffico orientato alla connessione da traffico datagram.              |
| `tcp.port`, `udp.port`                                               | Porta sorgente o destinazione del protocollo di trasporto.           | Cercare rapidamente un servizio, senza distinguere verso del traffico.             |
| `tcp.srcport`, `tcp.dstport` / `udp.srcport`, `udp.dstport`          | Porta sorgente / destinazione.                                       | Verificare chi offre il servizio e chi lo consuma; utile nei flussi client-server. |
| `tcp.flags`                                                          | Insieme dei flag TCP.                                                | Individuare SYN, ACK, FIN, RST e interpretare lo stato della connessione.          |
| `tcp.flags.syn`, `tcp.flags.ack`, `tcp.flags.fin`, `tcp.flags.reset` | Singoli flag TCP già esposti come campi filtrabili.                  | Trovare handshake, chiusure anomale, reset, scansioni SYN.                         |
| `tcp.stream`                                                         | Identificatore del flusso TCP ricostruito da Wireshark.              | Isolare una singola conversazione TCP.                                             |
| `udp.stream`                                                         | Identificatore del flusso UDP, quando disponibile.                   | Seguire scambi UDP specifici.                                                      |
| `eth`, `eth.addr`, `eth.src`, `eth.dst`                              | Campi Ethernet / MAC address.                                        | Analisi LAN, ARP, switch, host fisici, troubleshooting locale.                     |
| `frame.len`                                                          | Lunghezza totale del frame.                                          | Cercare pacchetti anomali, jumbo frame, burst insoliti.                            |
| `frame.number`                                                       | Numero progressivo del frame nel capture file.                       | Saltare a frame precisi, correlare note o analisi manuali.                         |
| `frame contains` / `data.data contains`                              | Cerca byte o stringhe nel frame/payload.                             | Ricerca di flag, stringhe, indicatori testuali, pattern grezzi.                    |
| `arp`                                                                | Filtra pacchetti ARP.                                                | Troubleshooting locale, risoluzione MAC, spoofing ARP.                             |
| `icmp`, `icmpv6`                                                     | Filtra messaggi ICMP / ICMPv6.                                       | Ping, unreachable, traceroute, errori di rete.                                     |
| `dns`                                                                | Filtra traffico DNS.                                                 | Analizzare risoluzioni nomi, esfiltrazione DNS, domini sospetti.                   |
| `dns.qry.name`                                                       | Nome richiesto nella query DNS.                                      | Cercare uno specifico dominio o pattern di sottodomini.                            |
| `dns.flags.response`                                                 | Indica se il pacchetto DNS è query o risposta.                       | Separare richieste e risposte.                                                     |
| `http`                                                               | Filtra traffico HTTP decodificato.                                   | Analisi web in chiaro o traffico proxy.                                            |
| `http.host`                                                          | Header `Host` HTTP.                                                  | Cercare un dominio richiesto in HTTP.                                              |
| `http.request.method`                                                | Metodo HTTP (`GET`, `POST`, ecc.).                                   | Individuare login, upload, download, API call.                                     |
| `http.request.uri`                                                   | URI richiesto.                                                       | Cercare endpoint, path sospetti, pagine sensibili.                                 |
| `tls`                                                                | Filtra traffico TLS/SSL decodificato.                                | Individuare sessioni cifrate.                                                      |
| `tls.handshake.type`                                                 | Tipo di messaggio di handshake TLS.                                  | Analizzare ClientHello, ServerHello, certificati.                                  |
| `tls.handshake.extensions_server_name`                               | SNI presentato nel ClientHello.                                      | Vedere il nome host richiesto anche senza decifrare HTTPS.                         |
| `dhcp`                                                               | Filtra traffico DHCP.                                                | Assegnazione IP, starvation, rogue DHCP, troubleshooting bootstrap.                |
| `bootp.option.type`                                                  | Tipo di opzione DHCP/BOOTP.                                          | Controllare parametri distribuiti dal server.                                      |
| `ftp`, `ftp-data`                                                    | Traffico di controllo FTP e dati FTP.                                | Verificare autenticazioni o trasferimenti separati.                                |
| `smtp`, `pop`, `imap`                                                | Protocolli email classici.                                           | Analisi posta in chiaro o laboratorio didattico.                                   |
| `ssh`                                                                | Traffico SSH.                                                        | Isolare accessi remoti sicuri; vedere pattern e timing, non il contenuto.          |
| `ntp`                                                                | Network Time Protocol.                                               | Verificare sincronizzazione oraria o anomalie temporali.                           |
| `sip`, `rtp`                                                         | Segnalazione VoIP e traffico audio.                                  | Analizzare chiamate VoIP e relativo flusso media.                                  |
| `wlan`, `wlan.fc.type_subtype`                                       | Campi del traffico Wi‑Fi 802.11.                                     | Beacon, probe, auth, assoc, deauth, analisi wireless.                              |
| `wlan.addr`                                                          | Indirizzo MAC coinvolto in frame Wi‑Fi.                              | Trovare tutto il traffico legato a un dispositivo wireless.                        |

## 3) Mini-pattern utili

```wireshark
ip.addr == 192.168.1.10
```

Tutto il traffico relativo a un host.

```wireshark
tcp.port in {80 443 8080}
```

Traffico web più comune.

```wireshark
dns && dns.qry.name contains "example"
```

Query DNS che contengono una certa stringa.

```wireshark
tcp.flags.syn == 1 && tcp.flags.ack == 0
```

Pacchetti SYN iniziali, utile per handshake o scansioni.

```wireshark
http.request.method == "POST"
```

Richieste HTTP POST.

```wireshark
tls.handshake.extensions_server_name contains "google"
```

Connessioni TLS verso host con SNI corrispondente.

```wireshark
icmp || dns
```

Solo ICMP o DNS.

```wireshark
!(arp || dns || icmp)
```

Esclude protocolli molto rumorosi in catture semplici.
