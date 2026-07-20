# Læringslogg – Juniorkonsulent IT og Automatisering (Otech Consult AS)

Format per uke: dato, hva som ble gjort, utfordringer, neste steg.

---

## 20.07.2026 – Fase 5: Ansible – første playbook og nginx-installasjon

**Mål:** Sette opp Ansible, forstå grunnbegrepene (inventory, playbook, tasks), og bruke det til å automatisere installasjon og oppstart av en tjeneste.

**Miljøoppsett:**
- Opprettet mappe ~/fase5-ansible på VM-en
- Satt opp virtuelt miljø (venv) og installert Ansible via pip (samme fremgangsmåte som Python-venv fra Fase 4)
- Bekreftet installasjon: Ansible core 2.21.2

**Grunnbegreper testet:**
- Laget inventory.ini med gruppen [lokale] og localhost som eneste host (ansible_connection=local)
- Testet tilkobling med ansible -m ping → SUCCESS
- Kjørte første ad-hoc-kommando (whoami) direkte fra kommandolinjen

**Første playbook:**
- Skrev forste-playbook.yml med enkel task (whoami) + debug-utskrift av resultatet, brukte register for å lagre og gjenbruke output
- Kjørte med ansible-playbook -i inventory.ini forste-playbook.yml → alle tasks OK

**Automatisert nginx-installasjon:**
- Utvidet playbooken til å installere og starte nginx med apt- og service-modulene
- become: yes brukt for å kjøre tasks med root-rettigheter

**Feilsøking underveis – become/sudo-passord:**
- --ask-become-pass feilet gjentatte ganger med "Timed out waiting for become success" – trolig VS Code sin integrerte terminal som håndterer det interaktive passord-promptet dårlig
- Løsning: satt opp passordløs sudo for brukeren filip via /etc/sudoers.d/filip (NOPASSWD:ALL) – bevisst valg, kun trygt fordi dette er en isolert lab-VM, ikke noe som gjøres på produksjonssystemer
- Etter dette kjørte playbooken uten problemer

**Resultat:**
- nginx installert og verifisert kjørende (systemctl status nginx → active running)
- Bekreftet fungerende webserver med curl http://localhost → fikk nginx sin standard velkomstside

**Læring:**
- Ansible sin styrke er gjentakbarhet – playbooken kan kjøres om igjen uten å "ødelegge" noe (apt-modulen installerer bare hvis pakken ikke allerede finnes)
- become: yes er Ansibles versjon av sudo, men krever enten interaktivt passord (kan være upraktisk i enkelte terminaler) eller passordløs sudo-konfigurasjon for automatisering
- register + debug er nyttig for å se og bruke resultatet av en task videre i playbooken
- Neste steg: roller og templates for mer strukturerte, gjenbrukbare playbooks

---

## 18.07.2026 – Fase 4: Funksjoner, feilhåndtering og API-kall (sluttprosjekt)

**Mål:** Fullføre kjernen av Fase 4 med funksjoner, feilhåndtering (try/except) og et script som henter data fra et eksternt API – sluttmålet for hele fasen ifølge opprinnelig plan.

**Funksjoner:**
- Definerte egen funksjon logg_oppgave(tekst) som pakker inn tidsstempel + filskriving
- Kalte samme funksjon flere ganger med ulik input, uten å gjenta kode

**Feilhåndtering:**
- Introduserte try/except for å fange feil (f.eks. ValueError ved ugyldig input) uten at scriptet krasjer
- Logget feilinput automatisk via logg_oppgave() inni except-blokken
- Diskuterte forskjellen på if/else (sjekke kjente betingelser på forhånd) vs. try/except (håndtere uforutsette feil som oppstår underveis, f.eks. ved filer, nettverk, brukerinput)

**Miljøproblem underveis – virtuelt miljø (venv):**
- pip install requests feilet først med "externally-managed-environment" (Ubuntu beskytter systemets Python fra direkte pip-installasjon)
- Opprettet virtuelt miljø: python3 -m venv venv – første forsøk ga en ufullstendig venv-mappe (manglet activate, pip osv.)
- Årsak: den generiske pakken python3-venv installerte ikke riktig binding for Python 3.14
- Løsning: fant og installerte riktig versjonsspesifikk pakke: sudo apt install python3.14-venv, slettet den ødelagte venv-mappen og opprettet den på nytt
- Aktiverte med source venv/bin/activate, installerte deretter requests uten problemer

**API-kall (sluttprosjekt for Fase 4):**
- Skrev script som henter sanntids værdata for Sandnes fra Open-Meteo (gratis, åpent API, ingen nøkkel påkrevd)
- Brukte requests.get(url) til å sende forespørsel, .json() til å parse JSON-svaret
- Hentet ut spesifikke verdier (temperatur, vindhastighet) fra den nøstede JSON-strukturen
- La til try/except requests.exceptions.RequestException for å håndtere nettverksfeil
- Logget resultatet av hver kjøring til logg.txt via logg_oppgave()

**Resultat:**
- Fungerende script som henter, viser og logger ekte værdata fra internett
- Alle kjernekonsepter i Fase 4 nå dekket: variabler, kontrollflyt, løkker, filbehandling, funksjoner, feilhåndtering, API-kall
- Lærte i tillegg om virtuelle miljøer (venv) – ikke opprinnelig planlagt, men en viktig og vanlig del av ekte Python-arbeidsflyt

**Læring:**
- Virtuelle miljøer isolerer prosjekt-spesifikke pakker fra resten av systemet – dette er standard praksis, ikke bare en snarvei for å unngå en feilmelding
- venv må aktiveres på nytt (source venv/bin/activate) i hver ny terminal-økt – den følger ikke automatisk med
- try/except er nødvendig når man jobber med noe utenfor egen kontroll (nettverk, filer, brukerinput) – utfyller if/else, erstatter det ikke
- API-er returnerer strukturert JSON-data som må "navigeres" med riktige nøkler basert på API-ets dokumentasjon

---

## 18.07.2026 – Fase 4: Python-grunnlag og filbehandling

**Mål:** Sette opp Python-utviklingsmiljø via VS Code Remote-SSH mot Ubuntu-VM-en, og dekke grunnleggende Python-syntaks samt filbehandling.

**Miljøoppsett:**
- Installerte VS Code-utvidelsen "Remote - SSH"
- Satt opp SSH-config for tilkobling til VM-en (samme port forwarding som fra Fase 1: localhost:2222)
- Løste en midlertidig nedlastingsfeil av VS Code Server (løst med Retry - antas å ha vært en forbigående nettverksglipp)
- Opprettet mappe ~/fase4-python på VM-en for alt Python-arbeid
- Bekreftet Python 3.14.4 installert

**Python-konsepter dekket (repetisjon + noe nytt):**
- Variabler, datatyper (string, int) og f-strings
- input() og typekonvertering med int()
- if/else-betingelser og innrykk (indentation)
- for-løkker, lister og range()
- Modulo-operator (%) for partall/oddetall-sjekk

**Filbehandling (nytt fokus for denne økten):**
- Skrive til fil med open(..., "w") og f.write()
- Lese fra fil med open(..., "r") og f.read()
- Legge til i eksisterende fil med open(..., "a") (append, uten å overskrive)
- Kombinerte input + filskriving + tidsstempel via datetime.now().strftime(...) til et enkelt loggeskript

**Resultat:**
- Fungerende script som tar imot input fra bruker og logger det med tidsstempel til logg.txt
- Alle øvelser kjørt og verifisert direkte i VS Code sin integrerte terminal via Remote-SSH

**Læring:**
- VS Code Remote-SSH gir samme redigeringsopplevelse som lokalt, men filene ligger fysisk på VM-en – nyttig verktøy videre i Fase 5/6
- "w" overskriver hele filen, "a" legger til – viktig å velge riktig modus for å unngå å miste data
- with open(...) as f: er tryggere enn å åpne filer manuelt, siden filen lukkes automatisk selv ved feil
- Konseptuelt ligner automatisert logging (dette scriptet) på det som gjøres manuelt i laeringslogg.md – naturlig bro mot senere automatisering i Ansible

---

## 17.07.2026 – Fase 3: Branch, merge og konfliktløsning

**Mål:** Gjennomføre en fullstendig branch/merge-arbeidsflyt, inkludert å lage og løse en bevisst merge-konflikt, for å fullføre Fase 3 (Git) i tråd med opprinnelig plan.

**Fremgangsmåte:**
- Opprettet en ny branch: `git checkout -b test-branch`
- Gjorde en endring i `laeringslogg.md` på `test-branch`, committet
- Byttet tilbake til `main`: `git checkout main`
- Gjorde en *annen* endring på samme linje i `laeringslogg.md` på `main`, committet
- Kjørte `git merge test-branch` fra `main` → utløste bevisst en merge-konflikt

**Løsning av konflikten:**
- Åpnet filen i VS Code og fant konfliktmarkørene (`<<<<<<<`, `=======`, `>>>>>>>`)
- Redigerte filen manuelt for å beholde ønsket innhold, fjernet markørene
- Fullførte merge med `git add` + `git commit`
- Slettet `test-branch` etter vellykket merge (`git branch -d test-branch`)
- Pushet resultatet til GitHub

**Øvrig:**
- Oppdaterte `README.md` med oversikt over alle 7 faser og status
- Ryddet bort testlinjene som ble brukt til å fremprovosere konflikten

**Resultat:**
- Merge-konflikt løst korrekt, verifisert med `git log` og bekreftet på GitHub etter push
- Fase 3 (Git) er nå fullt dekket: commit/branch/merge, konfliktløsning, gode commit-meldinger, README

**Læring:**
- Konfliktmarkørene (`<<<<<<<`, `=======`, `>>>>>>>`) viser tydelig hvilken branch som eide hvilken versjon av endringen
- Å løse en konflikt er i praksis bare å redigere filen manuelt til ønsket sluttresultat, så committe som normalt
- Branch-basert arbeidsflyt lar deg eksperimentere trygt uten å påvirke `main` før du er klar til å slå sammen

---

## 17.07.2026 – Fase 2: DNS-oppsett og test på tvers av VLAN

**Mål:** Sette opp en enkel DNS-tjeneste og bekrefte navneoppslag fungerer, inkludert på tvers av VLAN.

**Konfigurasjon:**
- Lagt til en Server (Server-PT) koblet til switch, port FastEthernet0/6
- Satt Fa0/6 til access-port i VLAN 10 (switchport access vlan 10)
- Server fikk statisk IP 192.168.10.100, gateway 192.168.10.254
- Aktivert DNS-tjeneste på serveren, lagt til A-record: test.otech.local → 192.168.10.100
- Lagt til dns-server 192.168.10.100 i begge DHCP-poolene (VLAN10 og VLAN20) på ruteren
- Fornyet DHCP-lease på PC-ene for å hente ny DNS-info

**Test:**
- ping test.otech.local fra PC0 (VLAN 10) → OK, løst til riktig IP
- ping test.otech.local fra PC2 (VLAN 20) → OK, bekrefter DNS-oppslag fungerer på tvers av VLAN

**Læring:**
- DNS-info kan distribueres automatisk til klienter via DHCP (dns-server-kommando i pool), i stedet for å settes manuelt på hver PC
- DNS-oppslag er avhengig av at underliggende routing (inter-VLAN) fungerer, siden PC2 må nå serveren i VLAN 10 for å få svar
- Fase 2s kjerneelementer (IPv4/subnetting, VLAN, DHCP, DNS, routing, Cisco IOS) er nå dekket praktisk gjennom disse øvelsene

---

## 17.07.2026 – Fase 2: DHCP for to VLAN

**Mål:** Sette opp automatisk IP-utdeling (DHCP) for begge VLAN via ruteren, i stedet for statisk IP på hver PC.

**Konfigurasjon på ruter:**
- Ekskluderte gateway-adressene fra utdeling (ip dhcp excluded-address 192.168.10.254 og 192.168.20.254)
- Opprettet DHCP pool VLAN10: network 192.168.10.0/24, default-router 192.168.10.254
- Opprettet DHCP pool VLAN20: network 192.168.20.0/24, default-router 192.168.20.254
- Lagret konfigurasjon med write memory

**Feil underveis:**
- Skrev ved en feil 192.169.10.0 i stedet for 192.168.10.0 i network-kommandoen for VLAN10-poolen
- Rettet opp ved å kjøre network-kommandoen på nytt inni samme pool

**Test:**
- Byttet alle 4 PC-er fra Static til DHCP i IP Configuration
- PC0 og PC1 fikk automatisk IP i 192.168.10.x-området
- PC2 og PC3 fikk automatisk IP i 192.168.20.x-området
- Ping fra PC0 til PC2 med nye DHCP-tildelte adresser → OK, bekrefter at ruting fortsatt fungerer med dynamiske adresser

**Læring:**
- DHCP-pool knyttes til riktig VLAN basert på hvilket subnett den er definert for, og matches mot hvilket VLAN forespørselen kommer inn fra (via ruterens subinterface)
- Det er switchporten (access vlan-konfigurasjon), ikke ruteren, som avgjør hvilket VLAN en tilkoblet enhet havner i
- En ukonfigurert switchport havner som standard i VLAN 1, som ikke har DHCP-pool eller kontakt med VLAN 10/20 – nye porter må konfigureres eksplisitt

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
