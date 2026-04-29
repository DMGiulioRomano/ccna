---
title: "Capitolo 13"
author: "Giulo Romano De Mattia via Deepseek"
date: "2026-04-29"
lang: it-IT
subject: "Protocollo ICMP"
keywords: [protocol, icmp, ccna, ping, tracert, traceroute]
geometry: margin=2.5cm
---

#  MODULO ICMP – TEST DELLA CONNETTIVITÀ DI RETE

##  Obiettivo del modulo  
*Use various tools to test network connectivity* → usare **ping** e **traceroute** per verificare se e come i pacchetti viaggiano in rete.

---

##  TOPIC 1: ICMP Messages

###  Cos’è ICMP?
**ICMP** = **Internet Control Message Protocol** (protocollo di messaggistica di controllo).  
Non trasporta dati utente (come TCP/UDP), ma **messaggi di diagnostica, errore e controllo** tra dispositivi di rete (router, host).

>  *“ICMP è il vigile urbano della rete: segnala problemi, dice se una strada è chiusa, o se un nodo non risponde.”*

### Come ICMP aiuta a testare la connettività?
ICMP viene usato da due strumenti fondamentali:

| Strumento      | Tipo di messaggio ICMP                | Scopo                                   |
|----------------|----------------------------------------|------------------------------------------|
| `ping`         | Echo Request (tipo 8) + Echo Reply (tipo 0) | Verificare se un host è raggiungibile e misurare RTT |
| `traceroute`   | Echo Request con TTL crescente + Time Exceeded (tipo 11) | Mappare il percorso dei pacchetti (ogni router salta fuori) |

###  Altri messaggi ICMP importanti (per CCNA)
- **Destination Unreachable (tipo 3)** – rete/host/porta non raggiungibile.
- **Time Exceeded (tipo 11)** – TTL scaduto (usato da traceroute).
- **Redirect (tipo 5)** – il router dice “usa un altro gateway” (solo in reti locali).
- **Parameter Problem (tipo 12)** – errore nell’header IP.

###  Attenzione: non tutto l’ICMP è uguale
- **Alcuni firewall bloccano Echo Request/Reply** → ping fallisce anche se la rete funziona.
- **Per i test usare** `ping` e `traceroute` **solo in ambienti controllati** o con permessi.

---

##  TOPIC 2: Ping and Traceroute Testing

###  PING – il termometro della rete

#### Sintassi base (Cisco IOS / Windows / Linux)
```bash
ping <IP o nome host>
ping 8.8.8.8
```

#### Cosa fa “ping”?
1. Invia un **ICMP Echo Request** all’IP di destinazione.
2. Il destinatario (se vivo) risponde con **ICMP Echo Reply**.
3. Calcola:
   - **RTT** (Round Trip Time)
   - **Percentuale di perdita**

#### Esempio di output (Windows)
```
Pinging 8.8.8.8 with 32 bytes of data:
Reply from 8.8.8.8: time=12ms TTL=117
Reply from 8.8.8.8: time=11ms TTL=117
...
Ping statistics:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
Approximate round trip times in milli-seconds:
    Minimum = 11ms, Maximum = 12ms, Average = 11ms
```

#### Interpretazione dei risultati
| Risultato                        | Significato                                      |
|----------------------------------|--------------------------------------------------|
| `Reply from ...`                 | OK – connettività bidirezionale                  |
| `Request timed out`              | Nessuna risposta (host spento, firewall, routing) |
| `Destination unreachable`        | ICMP type 3 → il router non sa raggiungere la destinazione |
| `TTL expired in transit`         | Il TTL è arrivato a zero (possibile loop o rete troppo grande) |

#### Ping esteso (Cisco IOS)
Da modalità enable:
```
ping
Protocol [ip]: ip
Target IP address: 10.0.0.1
Repeat count [5]: 100
Datagram size [100]: 1500
Timeout in seconds [2]:
Extended commands? [no]: yes
Source address or interface: 192.168.1.1
...
```
Utile per testare **MTU**, **sorgente specifica**, **rate** di pacchetti.

---

###  TRACEROUTE – la mappa stradale della rete

#### Su Windows: `tracert`
#### Su Linux/Cisco IOS: `traceroute`

#### Come funziona (magia ICMP+TTL)
1. Invia pacchetti con **TTL = 1** → primo router risponde con **Time Exceeded** → sappiamo l’IP del primo hop.
2. Invia con **TTL = 2** → secondo router risponde → secondo hop.
3. Ripete fino a raggiungere la destinazione (Echo Reply).

#### Esempio tracert (Windows)
```
C:\> tracert 8.8.8.8

  1    <1 ms    <1 ms    <1 ms  192.168.1.1
  2     5 ms     4 ms     5 ms  10.0.0.254
  3    12 ms    11 ms    12 ms  172.16.1.1
  4     *        *        *     Richiesta scaduta.
  5    22 ms    21 ms    20 ms  8.8.8.8
```

#### Cosa significano gli asterischi `*`
- Il router non risponde alle richieste ICMP (firewall/priorità).
- Non è necessariamente un problema, ma il percorso è oscurato.

#### Traceroute in Cisco IOS
```
Router# traceroute 8.8.8.8
Type escape sequence to abort.
Tracing the route to 8.8.8.8
  1 192.168.1.1 0 msec 0 msec 0 msec
  2 10.0.0.254 4 msec 4 msec 4 msec
  3 172.16.1.1 12 msec 12 msec 12 msec
  4  * * *
  5 8.8.8.8 20 msec 21 msec 20 msec
```

#### Parametri utili in Cisco
- `traceroute <ip> source <interface/ip>` – specifica sorgente.
- `traceroute <ip> numeric` – non risolve i nomi (più veloce).

---

##  Limitazioni reali (da sapere per l’esame CCNA)

| Problema                     | Effetto su ping/traceroute                          |
|-------------------------------|------------------------------------------------------|
| Firewall blocca ICMP          | Ping fallisce, traceroute mostra solo asterischi     |
| Routing asimmetrico           | Traceroute può mostrare percorsi diversi andata/ritorno |
| QoS o shaping                 | RTT variabile, possibile perdita pacchetti           |
| ICMP rate-limiting            | Alcuni pacchetti vengono persi senza motivo reale    |

---

##  Domande tipiche CCNA (stile esame)

1. **Quale messaggio ICMP viene usato da traceroute per identificare i router intermedi?**  
   → `Time Exceeded` (tipo 11, codice 0)

2. **Un ping verso un host nella stessa LAN fallisce con “Destination Host Unreachable”. Cosa può essere?**  
   → ARP fallita (host spento o subnet sbagliata) o switch VLAN errata.

3. **Differenza tra `ping` e `traceroute` in termini di ICMP?**  
   Ping usa Echo Request/Reply, traceroute usa pacchetti con TTL crescente per generare Time Exceeded.

4. **Perché un ping verso 8.8.8.8 funziona, ma traceroute si ferma al terzo hop con asterischi?**  
   → I router intermedi rispondono a Echo Request ma non generano Time Exceeded (o li bloccano).

---

##  Riepilogo per il tuo ripasso (da stampare mentale)

| Comando          | Utilità principale                          | Protocollo sottostante |
|------------------|----------------------------------------------|------------------------|
| `ping`           | Test raggiungibilità base + RTT              | ICMP Echo (tipo 8/0)   |
| `traceroute`     | Scoprire il percorso dei pacchetti           | ICMP Time Exceeded     |
| `extended ping`  | Test avanzati (sorgente, size, count)        | ICMP                   |

