# Læringslogg – Juniorkonsulent IT og Automatisering (Otech Consult AS)

Format per uke: dato, hva som ble gjort, utfordringer, neste steg.

---

## 17.07.2026 – Fase 2: VLAN og Router-on-a-Stick

**Mål:** Segmentere ett fysisk nettverk i flere logiske VLAN på én switch, og få en ruter til å rute trafikk mellom VLAN-ene over én enkelt kabel (router-on-a-stick).

**Topologi:**
- 1 Switch (2960) med 4 PC-er tilkoblet
- PC0, PC1 → VLAN 10 (192.168.10.0/24)
- PC2, PC3 → VLAN 20 (192.168.20.0/24)
- Router (1941) koblet til switch med én kabel, trunk-port

**Del 1 – VLAN-konfigurasjon på switch:**
- Opprettet VLAN 10 (navn: nett1) og VLAN 20 (navn: nett2)
- Satt FastEthernet0/1 og 0/2 til access-porter i VLAN 10
- Satt FastEthernet0/3 og 0/4 til access-porter i VLAN 20
- Verifisert med show vlan brief

**Test av isolasjon (før ruter):**
- PC0 til PC1 (samme VLAN) → OK
- PC0 til PC2 (ulikt VLAN) → Feilet som forventet (bekrefter at VLAN-ene er isolert fra hverandre)

**Del 2 – Router-on-a-stick:**
- Satt FastEthernet0/5 på switch til trunk-modus (switchport mode trunk)
- Opprettet subinterfaces på ruterens GigabitEthernet0/0:
  - 0/0.10 med encapsulation dot1Q 10, IP 192.168.10.254/24
  - 0/0.20 med encapsulation dot1Q 20, IP 192.168.20.254/24
- Aktivert hovedinterface med no shutdown
- Lagret konfigurasjon med write memory
- Satt Default Gateway på alle 4 PC-er (192.168.10.254 for VLAN 10, 192.168.20.254 for VLAN 20)

**Resultat:**
- Ping fra PC0 til PC2 (på tvers av VLAN) → OK etter at ruteren var konfigurert
- Ping fra PC0 til egen gateway → OK
- Bekreftet full ruting mellom VLAN 10 og VLAN 20 via router-on-a-stick

**Læring:**
- VLAN isolerer trafikk på Layer 2 selv om enhetene er på samme fysiske switch
- Trunk-porter sender tagget trafikk for flere VLAN over én kabel
- Subinterfaces lar én fysisk ruterport fungere som flere logiske gateway-adresser
- write memory må kjøres igjen etter ny ruterkonfigurasjon – samme lærdom som i forrige økt

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
