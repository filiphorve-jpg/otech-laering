# Læringslogg – Juniorkonsulent IT og Automatisering (Otech Consult AS)

Format per uke: dato, hva som ble gjort, utfordringer, neste steg.

---

## 16.07.2026 – Fase 2: Løst ping-problem mellom nettverk (routing fungerer)

**Mål:** Få inter-nettverk-ruting til å fungere mellom to nett via Cisco 1941-ruter i Packet Tracer.

**Topologi:**
- Nett 1 (192.168.1.0/24): PC0, PC1, PC2 + Switch → Router GigabitEthernet0/0
- Nett 2 (192.168.2.0/24): PC3 + Switch1 → Router GigabitEthernet0/1
- Router: Cisco 1941, gateway-adresser 192.168.1.254 og 192.168.2.254

**Problem:**
Ping mellom PC-er og ruter feilet vedvarende, til tross for at IP-adressering, subnettmaske, default gateway og portstatus var bekreftet korrekt på alle enheter. Simulation-modus i Packet Tracer viste at pakken stoppet ved ruteren. Mistanke om korrupt/ustabil Packet Tracer-økt.

**Løsning:**
- Avinstallerte og lastet ned Packet Tracer på nytt fra NetAcad
- Bygde topologien fra bunnen av igjen (PC-er med IP/gateway, kabling, ruterkonfigurasjon)
- Konfigurerte ruterens interfacer (GigabitEthernet0/0 med 192.168.1.254/24, GigabitEthernet0/1 med 192.168.2.254/24, begge satt til no shutdown)
- Lagret konfigurasjonen permanent med write memory (glemt sist – trolig forklarer det tidligere problemer)

**Resultat:**
- Ping fra PC0 til ruter (192.168.1.254) → OK
- Ping fra PC0 til motsatt gateway (192.168.2.254) → OK
- Ping fra PC0 til PC3 (192.168.2.10) → OK

Full ende-til-ende-kommunikasjon mellom de to nettverkene bekreftet. Inter-nettverk-ruting fungerer som forventet.

**Læring:**
- Alltid kjør write memory etter ruterkonfigurasjon i Packet Tracer
- Ved uforklarlige feil som ikke stemmer med korrekt konfig – vurder korrupt program-økt tidlig, ikke bare feilsøk konfigurasjonen i det uendelige
- Simulation-modus er nyttig for å se nøyaktig hvor en pakke stopper

---

## 13.07.2026 – Fase 2: Nettverk (Packet Tracer)

**Gjort:**
- Installert Cisco Packet Tracer, opprettet NetAcad-konto
- Bygget grunnleggende topologi: PC-er, switch, ruter med to subnett (192.168.1.0/24 og 192.168.2.0/24)
- Konfigurerte ruterens IP-adresser via CLI (interface, ip address, no shutdown)
- Lærte write memory / copy running-config startup-config for permanent lagring

**Utfordringer:**
- Vedvarende "no ping"-problem mellom PC og ruter til tross for korrekt konfigurasjon
- Systematisk feilsøkt: bekreftet IP/gateway på PC, portstatus på switch, MAC-tabell, 
  CDP-naboer, ARP-tabell - alt viste riktig konfigurasjon, men trafikk kom aldri gjennom
- Mistanke om Packet Tracer-programfeil, mulig konflikt med VirtualBox sine nettverksadaptere

**Lært:**
- Feilsøkingsmetodikk for nettverk: sjekk lag for lag (fysisk kobling -> switch MAC-tabell 
  -> ARP -> ruterkonfigurasjon)
- Ciscokonfigurasjon lagres kun i RAM til man kjører "write memory" - akkurat som Git 
  krever commit for at endringer skal bli permanente
- show ip interface brief, show mac address-table, show cdp neighbors, arp -a som 
  diagnostikkverktøy

**Neste steg:**
- Avinstaller/installer Packet Tracer på nytt
- Fullføre subnetting/VLAN-øvelsen når verktøyet fungerer stabilt

---

## 13.07.2026 – Fase 1 (Windows) + Fase 3 (Git)

**Gjort:**
- Satt opp VirtualBox og Ubuntu Server (x86/AMD64) på Windows-PC
- Installerte openssh-server manuelt (var ikke inkludert i unattended install)
- Satt opp port forwarding (host port 2222 -> guest port 22) for SSH
- Genererte SSH-nøkkelpar med ssh-keygen, kopierte offentlig nøkkel til VM-en
- Installerte Git, opprettet GitHub-repo "otech-laering"
- Første git add/commit/push av laeringslogg.md

**Utfordringer:**
- ssh.service fantes ikke etter installasjon – måtte installeres og startes manuelt
- Første SSH-forsøk feilet ("Connection aborted") pga. manglende SSH-tjeneste
- ssh-keygen feilet første gang pga. krøllete anførselstegn fra kopiering

**Lært:**
- VirtualBox NAT-nettverk (10.0.2.x) krever port forwarding, ulikt UTM sitt bridged-nettverk på Mac
- SSH-nøkkelbasert innlogging er tryggere og mer "riktig" enn passordinnlogging
- git add -> commit -> push er hele grunnsyklusen for å lagre endringer

**Neste steg:**
- SSH-nøkler på Mac (utsatt fra forrige økt)
- Starte Fase 2: nettverk (subnetting, VLAN i Packet Tracer)

---

## 08.07.2026 – Fase 1: Grunnleggende IT (uke 1)

**Gjort:**
- Satt opp UTM og Ubuntu Server 26.04 LTS på Apple Silicon (M4)
- Installert systemoppdateringer (`sudo apt update && sudo apt upgrade -y`)
- Lært grunnleggende navigasjonskommandoer: `pwd`, `ls`, `ls -la`, `cd`, `cat`, `whoami`
- Koblet til VM-en via SSH fra Mac-terminalen (`ssh filip@192.168.64.2`)
- Slått av VM-en trygt via terminal (`sudo shutdown now`)

**Utfordringer:**
- Lasted ned feil ISO-variant først (+largemem), måtte bytte til standard ARM64
- Klipp/lim fungerte ikke pålitelig i UTM-vinduet – måtte skrive passord manuelt
- Skrev `apt update` i stedet for `apt upgrade` første gang (ingen skade, bare forsinkelse)

**Lært:**
- VM-vindu ≠ selve maskinen som kjører – kan lukkes uten å slå av VM-en
- Passord vises aldri i Linux-terminalen når man skriver/limer det inn (helt normalt)
- SSH lar deg styre en server uten å sitte "inni" den fysisk

**Neste steg:**
- Sette opp SSH-nøkkelbasert innlogging (uten passord)
- Sette opp Git-repo og flytte denne loggen dit

---

## [DATO] – [Fase / uke]

**Gjort:**
-

**Utfordringer:**
-

**Lært:**
-

**Neste steg:**
-
