# Læringslogg – Juniorkonsulent IT og Automatisering (Otech Consult AS)

Format per uke: dato, hva som ble gjort, utfordringer, neste steg.

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
