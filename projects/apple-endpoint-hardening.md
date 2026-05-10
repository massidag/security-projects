# 🍎 Hardening e Sicurezza Endpoint per Infrastrutture Apple

**Autore:** Massimo D'Aguanno ([@massidag](https://github.com/massidag))  
**Versione:** 1.0  
**Data:** Maggio 2026  
**Stato:** Documento finale  
**Certificazioni di riferimento:** ACMT · ACSP · ACIT · IBM IT Support Professional · IBM Cybersecurity Analyst (in corso)

---

## 📋 Indice

1. [Executive Summary](#1-executive-summary)
2. [Contesto e Obiettivi](#2-contesto-e-obiettivi)
3. [Analisi dei Rischi e Superficie di Attacco](#3-analisi-dei-rischi-e-superficie-di-attacco)
4. [Metodologia](#4-metodologia)
5. [Interventi Tecnici](#5-interventi-tecnici)
   - [5.1 Protezione del Dato e Crittografia](#51-protezione-del-dato-e-crittografia)
   - [5.2 Gestione delle Identità e degli Accessi](#52-gestione-delle-identità-e-degli-accessi)
   - [5.3 Integrità del Sistema e Difesa dalle Minacce](#53-integrità-del-sistema-e-difesa-dalle-minacce)
   - [5.4 Sicurezza di Rete](#54-sicurezza-di-rete)
   - [5.5 Gestione Centralizzata MDM](#55-gestione-centralizzata-mdm)
   - [5.6 Sicurezza Fisica e Controllo degli Accessi](#56-sicurezza-fisica-e-controllo-degli-accessi)
6. [Risultati e Metriche](#6-risultati-e-metriche)
7. [Raccomandazioni Post-Intervento](#7-raccomandazioni-post-intervento)
8. [Riferimenti e Standard](#8-riferimenti-e-standard)

---

## 1. Executive Summary

Questo documento descrive un intervento completo di **hardening della sicurezza** su un parco endpoint composto da dispositivi macOS e iOS, eseguito in un contesto aziendale reale.

L'analisi iniziale ha rilevato una superficie di attacco significativa: assenza di crittografia attiva, gestione frammentata degli accessi, configurazioni di rete non ottimizzate e nessuna policy centralizzata di sicurezza. L'intervento ha prodotto un'infrastruttura endpoint conforme alle best practice internazionali Apple e IBM Cybersecurity, con riduzione documentata dei principali vettori di rischio.

**Risultato sintetico:**

| Indicatore | Prima | Dopo |
|---|---|---|
| Endpoint con crittografia attiva | 0% | 100% |
| Account con privilegi minimi | 20% | 100% |
| Policy di sicurezza centralizzate | Assenti | Implementate via MDM |
| Firewall locale configurato | Parziale | Completo su tutti i nodi |
| MFA abilitato | Assente | Attivo su tutti gli account |

---

## 2. Contesto e Obiettivi

### Contesto operativo

L'intervento è stato condotto su un parco macchine aziendale composto da:

- Workstation **macOS** (MacBook Pro, MacBook Air, iMac) utilizzate per attività di produzione e accesso a dati sensibili
- Dispositivi mobili **iOS/iPadOS** con accesso a email aziendali, VPN e applicazioni gestionali
- Infrastruttura priva di una soluzione MDM (Mobile Device Management) preesistente
- Utenti con livelli di competenza eterogenei, alcuni con abitudini di sicurezza non consolidate

### Obiettivi del progetto

1. **Ridurre la superficie di attacco** su tutti gli endpoint Apple gestiti
2. **Proteggere i dati** a riposo e in transito contro accesso non autorizzato
3. **Centralizzare la gestione** delle policy di sicurezza tramite MDM
4. **Conformarsi** alle best practice Apple Platform Security e alle linee guida IBM Cybersecurity
5. **Documentare** ogni intervento per garantire tracciabilità e riproducibilità

---

## 3. Analisi dei Rischi e Superficie di Attacco

### 3.1 Metodologia di analisi

L'analisi è stata condotta attraverso:

- Ispezione manuale di ogni dispositivo
- Verifica delle configurazioni di sistema tramite terminale e System Preferences
- Revisione dei log di accesso e degli account utente
- Test di connettività e audit delle porte di rete aperte

### 3.2 Vulnerabilità identificate

#### 🔴 Critiche

| ID | Vulnerabilità | Impatto | Vettore |
|---|---|---|---|
| V-01 | FileVault disattivato su tutti i nodi macOS | Accesso fisico ai dati in chiaro in caso di furto o smarrimento del dispositivo | Fisico |
| V-02 | Account utente con privilegi amministrativi per uso quotidiano | Escalation di privilegi, installazione non autorizzata di software, malware con accesso root | Locale / Remoto |
| V-03 | Assenza di MFA su Apple ID e account aziendali | Compromissione account con sola password (credential stuffing, phishing) | Remoto |
| V-04 | Nessuna gestione centralizzata delle policy | Configurazioni eterogenee, impossibilità di applicare patch in modo uniforme | Organizzativo |

#### 🟠 Alte

| ID | Vulnerabilità | Impatto | Vettore |
|---|---|---|---|
| V-05 | Firewall locale disattivato o non configurato | Esposizione di servizi di rete non necessari | Rete locale |
| V-06 | Gatekeeper in modalità permissiva | Esecuzione di software non firmato o non verificato da Apple | Locale |
| V-07 | Aggiornamenti di sistema non applicati | Esposizione a vulnerabilità note con CVE pubblici | Remoto |
| V-08 | Condivisione file e servizi attivi non necessari (AirDrop, Sharing) | Superficie di attacco laterale nella rete locale | Rete locale |

#### 🟡 Medie

| ID | Vulnerabilità | Impatto | Vettore |
|---|---|---|---|
| V-09 | Password policy assente o debole | Brute force, accesso non autorizzato | Locale / Remoto |
| V-10 | Blocco schermo automatico non configurato | Accesso fisico non autorizzato a sessione attiva | Fisico |
| V-11 | Cifratura backup iTunes/Finder non attiva | Dati sensibili nei backup locali non protetti | Fisico |
| V-12 | System Integrity Protection (SIP) disattivato su alcuni nodi | Modifiche non autorizzate ai file di sistema | Locale |

---

## 4. Metodologia

L'intervento è stato strutturato in tre fasi:

```
FASE 1 — AUDIT          FASE 2 — HARDENING          FASE 3 — VERIFICA
─────────────────       ───────────────────────      ──────────────────
Inventario endpoint  →  Applicazione controlli   →   Test e validazione
Analisi rischi          per priorità (Critica →       Documentazione
Baseline sicurezza      Alta → Media)                 Piano manutenzione
```

**Principio guida:** ogni controllo applicato segue il framework **CIS Apple macOS Benchmark** e le linee guida **Apple Platform Security**, con priorità ai controlli che riducono il rischio più elevato con il minor impatto sulla produttività degli utenti.

---

## 5. Interventi Tecnici

### 5.1 Protezione del Dato e Crittografia

#### FileVault 2 — macOS

**Problema (V-01):** Tutti i dispositivi macOS avevano il disco fisso non cifrato. In caso di furto fisico del dispositivo, i dati erano accessibili semplicemente rimuovendo il disco o avviando da volume esterno.

**Soluzione applicata:**

Attivazione di **FileVault 2** su tutti i nodi macOS con gestione centralizzata delle chiavi di recupero.

```bash
# Verifica stato FileVault da terminale
fdesetup status

# Output atteso post-intervento
FileVault is On.
```

**Configurazione chiavi di recupero:**
- Generazione di una **Personal Recovery Key (PRK)** univoca per dispositivo
- Archiviazione sicura delle PRK in un vault aziendale protetto (non sul dispositivo stesso)
- Abilitazione del recupero istituzionale tramite certificato MDM per i dispositivi gestiti

**Impatto:** Dati completamente inaccessibili senza credenziali utente o chiave di recupero, anche in caso di furto fisico del dispositivo o rimozione del disco.

---

#### Cifratura iOS/iPadOS

**Problema:** La cifratura hardware di iOS è attiva di default ma condizionata all'impostazione di un passcode. Su alcuni dispositivi il passcode era assente o di lunghezza insufficiente (4 cifre).

**Soluzione applicata:**

- Verifica dell'attivazione della protezione dati tramite **Data Protection** (richiede passcode attivo)
- Imposizione tramite profilo MDM di passcode alfanumerico con minimo 8 caratteri
- Abilitazione **cancellazione automatica** dopo 10 tentativi falliti

```
Impostazioni → Face ID e Codice → Cancella dati: ON
```

**Verifica dello stato di protezione dati:**
```bash
# Su dispositivo con accesso sviluppatore
ideviceinfo -k DataProtectionCapability
# Output atteso: 1
```

---

#### Cifratura Backup

**Problema (V-11):** I backup locali su Finder/iTunes non erano cifrati, esponendo dati sensibili (password salvate, token di autenticazione, dati sanitari) in chiaro sul computer di backup.

**Soluzione applicata:**
- Attivazione di **Encrypt local backup** per tutti i dispositivi iOS gestiti
- I backup cifrati proteggono anche le password del portachiavi, i dati sanitari e i dati di HomeKit

---

### 5.2 Gestione delle Identità e degli Accessi

#### Privilege Management — Principio del minimo privilegio

**Problema (V-02):** Il 80% degli utenti operava con un account amministratore per le attività quotidiane. Questo significa che qualsiasi processo avviato dall'utente — incluso malware — poteva operare con privilegi di root.

**Soluzione applicata:**

Creazione di una struttura account a due livelli su ogni macOS:

```
┌─────────────────────────────────────────┐
│  Account Amministratore (nascosto)       │
│  → Solo per operazioni di sistema        │
│  → Non utilizzato per lavoro quotidiano  │
├─────────────────────────────────────────┤
│  Account Utente Standard (uso quotidiano)│
│  → Email, browser, applicazioni          │
│  → Richiede autenticazione admin per     │
│    installazioni e modifiche di sistema  │
└─────────────────────────────────────────┘
```

**Procedura applicata:**
1. Creazione nuovo account amministratore dedicato con password complessa
2. Declassamento degli account esistenti da Amministratore a Standard
3. Comunicazione agli utenti della nuova procedura per richieste di installazione software

**Impatto:** Un eventuale malware o attaccante che compromette la sessione utente non ottiene privilegi di sistema automaticamente.

---

#### Password Policy

**Problema (V-09):** Nessun requisito minimo di complessità delle password. Rilevate password di 4-6 caratteri, alcune identiche all'username.

**Soluzione applicata tramite MDM:**

```xml
<!-- Profilo MDM — Passcode Policy -->
<key>minLength</key><integer>12</integer>
<key>requireAlphanumeric</key><true/>
<key>minComplexChars</key><integer>2</integer>
<key>maxPINAgeInDays</key><integer>90</integer>
<key>pinHistory</key><integer>5</integer>
<key>maxFailedAttempts</key><integer>10</integer>
```

**Requisiti imposti:**
- Lunghezza minima: 12 caratteri
- Caratteri alfanumerici obbligatori con almeno 2 caratteri speciali
- Rotazione ogni 90 giorni
- Impossibilità di riutilizzare le ultime 5 password

---

#### Autenticazione a Più Fattori (MFA)

**Problema (V-03):** Nessun account aveva MFA attivo. Le credenziali erano il solo fattore di autenticazione.

**Soluzione applicata:**

- Attivazione **Two-Factor Authentication** su tutti gli Apple ID aziendali
- Configurazione di app authenticator (non SMS) come secondo fattore dove supportato
- Documentazione del processo di recovery per evitare lockout

**Verifica stato 2FA Apple ID:**
```
Impostazioni → [Nome account] → Password e sicurezza → 
Autenticazione a due fattori: Attiva
```

---

#### Blocco Schermo Automatico

**Problema (V-10):** Nessuna configurazione di blocco automatico. Dispositivi lasciati incustoditi con sessione attiva accessibile.

**Soluzione applicata:**
- macOS: blocco schermo automatico dopo **2 minuti** di inattività
- iOS/iPadOS: blocco automatico dopo **30 secondi**
- Richiesta password immediata alla riattivazione (eliminato il grace period)

```bash
# Verifica configurazione screensaver macOS
defaults read com.apple.screensaver idleTime
# Output atteso: 120 (secondi)

defaults read com.apple.screensaver askForPasswordDelay  
# Output atteso: 0 (nessun grace period)
```

---

### 5.3 Integrità del Sistema e Difesa dalle Minacce

#### System Integrity Protection (SIP)

**Problema (V-12):** SIP risultava disattivato su alcuni dispositivi, probabilmente a seguito di interventi tecnici precedenti mai ripristinati. SIP protegge le directory critiche di sistema da modifiche non autorizzate, anche da parte di processi con privilegi root.

**Verifica e ripristino:**
```bash
# Verifica stato SIP
csrutil status
# Output atteso: System Integrity Protection status: enabled.

# Se disattivato: riavvio in Recovery Mode → Utilities → Terminal
csrutil enable
```

**Impatto:** Con SIP attivo, nessun processo — incluso malware con root — può modificare directory di sistema come `/System`, `/usr`, `/bin`, `/sbin`.

---

#### Gatekeeper

**Problema (V-06):** Gatekeeper configurato per consentire l'apertura di applicazioni da "Qualsiasi sorgente", bypassando i controlli di firma e notarizzazione Apple.

**Soluzione applicata:**

```bash
# Verifica configurazione Gatekeeper
spctl --status
# Output atteso: assessments enabled

# Ripristino configurazione restrittiva
sudo spctl --master-enable
```

**Configurazione imposta:**
- Solo applicazioni dal Mac App Store e sviluppatori identificati con firma valida
- Blocco automatico di qualsiasi eseguibile non firmato o non notarizzato da Apple

---

#### XProtect e MRT

**Problema (V-07):** Aggiornamenti delle definizioni malware di XProtect non applicati su alcuni nodi (versioni obsolete di 3-6 mesi).

**Soluzione applicata:**
- Verifica e aggiornamento manuale delle definizioni XProtect su tutti i nodi
- Configurazione tramite MDM degli aggiornamenti automatici delle definizioni di sicurezza

```bash
# Verifica versione XProtect
system_profiler SPInstallHistoryDataType | grep -A 3 "XProtect"

# Forza aggiornamento definizioni sicurezza
sudo softwareupdate --background
```

---

#### Patch Management

**Problema (V-07):** Aggiornamenti di sistema disattivati o gestiti manualmente con ritardi significativi (alcuni dispositivi con versioni macOS con CVE critici noti e non patchati).

**Soluzione applicata:**
- Attivazione aggiornamenti automatici per patch di sicurezza critiche
- Configurazione MDM per forzare l'installazione entro 72 ore dalla disponibilità di patch critiche
- Inventario delle versioni OS su tutti i dispositivi con piano di aggiornamento progressivo

---

### 5.4 Sicurezza di Rete

#### Firewall Locale macOS

**Problema (V-05):** Il firewall integrato di macOS (Application Layer Firewall) era disattivato sulla maggior parte dei dispositivi, lasciando aperta la ricezione di connessioni in entrata non richieste.

**Soluzione applicata:**

```bash
# Attivazione firewall da terminale
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --setglobalstate on

# Attivazione stealth mode (nessuna risposta a probe ICMP non richiesti)
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --setstealthmode on

# Blocco connessioni in entrata per software non firmato
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --setblockall off
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --setallowsigned on

# Verifica configurazione
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --getglobalstate
# Output atteso: Firewall is enabled. (State = 1)
```

**Stealth Mode:** Con questa configurazione il dispositivo non risponde a richieste di connessione non richieste né a ping ICMP, risultando invisibile a scan di rete non autorizzati.

---

#### Disattivazione Servizi di Rete Non Necessari

**Problema (V-08):** Diversi servizi di condivisione attivi per abitudine o configurazioni pregresse mai revisionate.

**Servizi disattivati:**

| Servizio | Rischio | Stato Post-Intervento |
|---|---|---|
| File Sharing (SMB/AFP) | Accesso non autorizzato al filesystem | Disattivato |
| Screen Sharing | Controllo remoto non autorizzato | Disattivato |
| Remote Login (SSH) | Accesso shell remoto | Disattivato (abilitato solo su nodi amministrativi dedicati) |
| AirDrop | Ricezione file da sconosciuti | Solo Contatti |
| Bluetooth | Pairing non autorizzato | Disattivato quando non in uso |
| Remote Management | Accesso Apple Remote Desktop | Disattivato |

```bash
# Verifica servizi attivi
sudo launchctl list | grep -E "sharing|remote|bluetooth"

# Disattivazione File Sharing
sudo launchctl unload -w /System/Library/LaunchDaemons/com.apple.smbd.plist
```

---

#### Configurazione AirDrop

**Problema:** AirDrop configurato per ricevere da "Tutti" — vettore noto per attacchi di social engineering (AirDrop phishing).

**Soluzione:**
```bash
# Imposta AirDrop su "Solo Contatti"
defaults write com.apple.sharingd DiscoverableMode -string "Contacts Only"
```

---

### 5.5 Gestione Centralizzata MDM

#### Implementazione MDM

**Problema (V-04):** Assenza totale di gestione centralizzata. Ogni dispositivo aveva configurazioni diverse, impossibilità di applicare policy uniformi, nessuna visibilità centralizzata sullo stato di sicurezza del parco macchine.

**Soluzione applicata:**

Implementazione di una soluzione **MDM** per la gestione centralizzata di tutti i dispositivi Apple con enrollment tramite **Apple Business Manager (ABM)**.

**Profili di configurazione implementati:**

```
MDM
├── Profilo Base Sicurezza
│   ├── Password Policy (lunghezza, complessità, scadenza)
│   ├── Blocco schermo automatico
│   └── Cifratura backup obbligatoria
├── Profilo Rete
│   ├── Configurazione VPN aziendale
│   ├── Certificati Wi-Fi aziendali (WPA2-Enterprise)
│   └── Blocco reti Wi-Fi non autorizzate
├── Profilo Restrizioni
│   ├── Blocco installazione app non approvate
│   ├── Disattivazione servizi non necessari
│   └── Controllo AirDrop e Bluetooth
└── Profilo Aggiornamenti
    ├── Forza patch sicurezza critiche
    └── Notifiche aggiornamenti OS pendenti
```

**Funzionalità di risposta agli incidenti abilitate:**
- **Remote Lock:** blocco remoto immediato in caso di furto o smarrimento
- **Remote Wipe:** cancellazione remota certificata dei dati
- **Location Tracking:** geolocalizzazione dispositivi (solo dispositivi aziendali, con policy comunicata agli utenti)

---

### 5.6 Sicurezza Fisica e Controllo degli Accessi

#### Protezione Fisica

| Controllo | Implementazione |
|---|---|
| Kensington Lock | Installati su workstation fisse in aree ad accesso non controllato |
| Etichettatura asset | Inventario fisico con etichette identificative su ogni dispositivo |
| Boot password (Firmware) | EFI password impostata su MacBook per prevenire avvio da volume esterno |

#### EFI/Firmware Password

**Problema:** Nessuna protezione del firmware. Qualsiasi utente con accesso fisico poteva avviare il dispositivo da USB esterno bypassando completamente macOS e FileVault.

**Soluzione (dispositivi Intel):**
```
Riavvio in Recovery Mode (Cmd+R) → Utilities → 
Startup Security Utility → Firmware Password → Set Firmware Password
```

**Nota:** Su Mac con Apple Silicon (M1/M2/M3) la protezione è gestita diversamente tramite il Secure Enclave e non richiede EFI password separata.

---

## 6. Risultati e Metriche

### 6.1 Scorecard di sicurezza

| Area | Vulnerabilità Risolte | Rischio Residuo |
|---|---|---|
| Crittografia dati | V-01, V-11 | Basso |
| Gestione identità e accessi | V-02, V-03, V-09, V-10 | Basso |
| Integrità sistema | V-06, V-07, V-12 | Basso |
| Sicurezza di rete | V-05, V-08 | Basso |
| Gestione centralizzata | V-04 | Basso |

### 6.2 Confronto stato pre/post intervento

| Controllo di Sicurezza | Prima | Dopo |
|---|---|---|
| FileVault attivo | ❌ 0% endpoint | ✅ 100% endpoint |
| MFA abilitato | ❌ Nessun account | ✅ Tutti gli account |
| Account con privilegi minimi | ❌ 20% | ✅ 100% |
| Firewall locale attivo | ❌ Parziale | ✅ 100% endpoint |
| SIP attivo | ⚠️ Parziale | ✅ 100% endpoint |
| Gatekeeper configurato | ⚠️ Permissivo | ✅ Restrittivo |
| Patch di sicurezza aggiornate | ❌ Frammentato | ✅ Gestito via MDM |
| Policy centralizzate | ❌ Assenti | ✅ MDM attivo |
| Blocco schermo automatico | ❌ Non configurato | ✅ 2 min macOS / 30 sec iOS |
| Servizi di rete non necessari | ❌ Attivi | ✅ Disattivati |

### 6.3 Riduzione della superficie di attacco

```
PRIMA                              DOPO
─────────────────────────────      ────────────────────────────
Vettori fisici:     ALTO    →      Vettori fisici:     BASSO
Vettori di rete:    ALTO    →      Vettori di rete:    BASSO
Vettori locali:     CRITICO →      Vettori locali:     BASSO
Vettori remoti:     ALTO    →      Vettori remoti:     BASSO
```

---

## 7. Raccomandazioni Post-Intervento

### Manutenzione continuativa

| Attività | Frequenza | Responsabile |
|---|---|---|
| Verifica aggiornamenti OS e patch sicurezza | Settimanale | IT Admin |
| Audit account e privilegi utente | Mensile | IT Admin |
| Revisione log di accesso e anomalie | Mensile | IT Admin |
| Test di ripristino da backup cifrato | Trimestrale | IT Admin |
| Verifica integrità configurazioni MDM | Trimestrale | IT Admin |
| Formazione utenti su phishing e social engineering | Annuale | IT Admin + HR |

### Miglioramenti futuri consigliati

1. **EDR (Endpoint Detection & Response):** Implementazione di una soluzione EDR dedicata per macOS (es. CrowdStrike Falcon, SentinelOne) per visibilità avanzata sulle minacce in tempo reale
2. **SIEM Integration:** Centralizzazione dei log di sistema in un SIEM aziendale per correlazione degli eventi e rilevamento anomalie
3. **Zero Trust Network Access:** Sostituzione della VPN tradizionale con un approccio ZTNA per l'accesso alle risorse aziendali
4. **Vulnerability Scanning periodico:** Scan regolari con strumenti come Nessus o OpenVAS per identificare nuove vulnerabilità
5. **Incident Response Plan:** Sviluppo di un piano formale di risposta agli incidenti specifico per endpoint Apple

---

## 8. Riferimenti e Standard

- [Apple Platform Security Guide](https://support.apple.com/guide/security/welcome/web) — Documentazione ufficiale Apple sulla sicurezza della piattaforma
- [CIS Apple macOS Benchmark](https://www.cisecurity.org/benchmark/apple_os) — Standard di hardening CIS per macOS
- [NIST SP 800-124](https://csrc.nist.gov/publications/detail/sp/800-124/rev-2/final) — Guidelines for Managing the Security of Mobile Devices in the Enterprise
- [Apple Business Manager](https://business.apple.com) — Piattaforma di gestione dispositivi Apple in ambito enterprise
- IBM Cybersecurity Analyst Professional Certificate — Coursera/IBM

---

## 👤 Autore

**Massimo D'Aguanno**  
IT Security Specialist | Apple Certified (ACMT · ACSP · ACIT)  
IBM IT Support Professional | IBM Cybersecurity Analyst (in corso)

🔗 [LinkedIn](https://www.linkedin.com/in/massidag) | 🐙 [GitHub](https://github.com/massidag) | 🏆 [TryHackMe](https://tryhackme.com/p/massidag)

---

*Documento redatto a scopo dimostrativo e portfolio professionale. I dati specifici sono stati anonimizzati.*
