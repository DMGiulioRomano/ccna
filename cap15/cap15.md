---
title: "Capitolo 15"
author: "Giulo Romano De Mattia via Deepseek"
date: "2026-04-29"
lang: it-IT
subject: "Livelli OSI: Application, Presentation e Session"
keywords: [OSI, application layer, presentation layer, session layer, HTTP, HTTPS, DNS, DHCP, TLS, encryption, compression, ccna]
geometry: margin=2.5cm
---

# Modulo 15 - Topic 1: Application, Presentation and Session Layer

Ottima scelta. Questo argomento copre i livelli 5, 6 e 7 del modello OSI, che nel modello TCP/IP collassano tutti nel singolo "Application Layer". Capire la distinzione e' fondamentale per l'esame.

---

## Il contesto: OSI vs TCP/IP

Prima di entrare nel dettaglio, e' cruciale tenere a mente questa mappatura:

```
Modello OSI          Modello TCP/IP
-----------          --------------
7 - Application  \
6 - Presentation  >  Application Layer
5 - Session      /
4 - Transport        Transport Layer
3 - Network          Internet Layer
2 - Data Link    \
1 - Physical      >  Network Access Layer
```

Cisco nel CCNA usa spesso entrambi i modelli. Devi saperli distinguere e mettere in relazione.

---

## Livello 7 - Application Layer (Applicazione)

### Cos'e' e cosa NON e'

Errore comune: pensare che il livello applicazione sia il programma che usi (Firefox, Outlook). Non e' cosi'.

Il livello 7 e' l'interfaccia tra il software applicativo e la rete. In altre parole, e' il protocollo che permette all'applicazione di comunicare, non l'applicazione stessa.

```
[Firefox]  <-- applicazione utente (non e' il livello 7)
    |
[HTTP/HTTPS]  <-- questo e' il livello 7
    |
[TCP]  <-- livello 4
```

### Funzioni principali

- Identificare la disponibilita' del partner di comunicazione
- Sincronizzare la comunicazione tra le applicazioni
- Stabilire l'accordo sulle procedure di gestione degli errori
- Determinare se esistono risorse di rete sufficienti

### Protocolli principali del livello 7

| Protocollo | Funzione | Porta |
|---|---|---|
| HTTP | Trasferimento pagine web | 80 |
| HTTPS | HTTP sicuro (TLS) | 443 |
| FTP | Trasferimento file | 20/21 |
| SFTP | FTP sicuro | 22 |
| SMTP | Invio email | 25 |
| POP3 | Ricezione email | 110 |
| IMAP | Ricezione email avanzata | 143 |
| DNS | Risoluzione nomi | 53 |
| DHCP | Assegnazione IP | 67/68 |
| SNMP | Gestione dispositivi | 161/162 |
| Telnet | Accesso remoto (non sicuro) | 23 |
| SSH | Accesso remoto sicuro | 22 |

---

## Livello 6 - Presentation Layer (Presentazione)

### Ruolo

Il livello 6 e' il "traduttore" dello stack. Si occupa di tre funzioni fondamentali:

**1. Formattazione (Format) dei dati**

Garantisce che i dati inviati da un sistema siano leggibili dal sistema ricevente, indipendentemente dal formato interno. Pensa a due computer che usano codifiche diverse per i caratteri: il livello 6 risolve questa incompatibilita'.

Esempi di formati gestiti:
- Testo: ASCII, Unicode (UTF-8)
- Immagini: JPEG, PNG, GIF
- Video: MPEG, AVI
- Documenti: HTML, XML, JSON

**2. Compressione (Compression)**

Riduce il numero di bit da trasmettere. Questo migliora l'efficienza della rete, specialmente su link lenti o congestionati.

```
Dati originali: 10.000 bit
Dati compressi: 6.000 bit  --> risparmio del 40% di banda
```

**3. Cifratura (Encryption)**

Protegge la riservatezza dei dati durante la trasmissione. Il livello 6 cifra i dati sul mittente e li decifra sul destinatario.

Esempio pratico: HTTPS usa TLS/SSL, che opera concettualmente a livello 6.

```
Dati in chiaro --> [Encryption L6] --> Dati cifrati --> rete --> [Decryption L6] --> Dati in chiaro
```

---

## Livello 5 - Session Layer (Sessione)

### Ruolo

Il livello 5 gestisce il dialogo tra due dispositivi. Stabilisce, mantiene e termina le sessioni di comunicazione.

Pensa a una telefonata: c'e' un momento in cui la chiami (stabilisci la sessione), parli (mantieni la sessione), e riattacchi (termini la sessione). Il livello 5 fa esattamente questo per le applicazioni.

### Le tre fasi di una sessione

```
FASE 1: Establishment (Apertura)
  Host A ----[richiesta sessione]----> Host B
  Host A <---[accettazione]---------- Host B
  Sessione stabilita

FASE 2: Data Transfer (Trasferimento)
  Scambio bidirezionale di dati
  Sincronizzazione tramite checkpoint

FASE 3: Termination (Chiusura)
  Host A ----[richiesta chiusura]----> Host B
  Sessione terminata ordinatamente
```

### Concetto di checkpoint (sincronizzazione)

Il livello 5 inserisce dei "punti di controllo" nel flusso dati. Se la trasmissione si interrompe, non e' necessario ricominciare da zero: si riparte dall'ultimo checkpoint valido.

Esempio pratico: durante il download di un file di grandi dimensioni, se la connessione cade, alcuni protocolli riprendono dal punto in cui si erano interrotti, non dall'inizio.

### Modalita' di dialogo (Dialog Control)

| Modalita' | Descrizione | Esempio |
|---|---|---|
| Simplex | Solo una direzione | Broadcast radio |
| Half-duplex | Entrambe le direzioni, non contemporanee | Walkie-talkie |
| Full-duplex | Entrambe le direzioni, simultaneamente | Telefonata normale |

---

## Come i tre livelli lavorano insieme

Ecco il flusso completo quando un browser richiede una pagina web:

```
Browser (applicazione utente)
         |
         v
[L7 - HTTP]         : definisce la richiesta GET /index.html
         |
         v
[L6 - TLS/SSL]      : cifra la richiesta (se HTTPS)
                      comprime i dati se configurato
         |
         v
[L5 - Session]      : gestisce la sessione TCP aperta
                      tiene traccia dello stato della connessione
         |
         v
[L4 - TCP]          : segmentazione, affidabilita', porte
         |
        ...
```

Sul lato ricevente, il processo avviene in senso inverso: la sessione e' riconosciuta (L5), i dati vengono decifrati e decompressi (L6), e la richiesta HTTP viene interpretata dal server web (L7).
