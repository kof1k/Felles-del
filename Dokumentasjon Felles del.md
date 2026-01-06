
## Innhold

1. Generell oversikt
2. Systemarkitektur
3. Konfigurasjon av virtuell maskin
4. Installasjon og konfigurasjon av webserver
5. Utvikling av velkomstside
6. Arbeid med Git og GitHub
7. Konfigurasjon av lokalt DNS
8. Refleksjon om IT-sikkerhet
9. Bruk av AI i læring

---

## Generell oversikt

### Prosjektets mål

Opprettelse av en lokal webserver basert på Ubuntu Server for hosting av webapplikasjoner ved bruk av virtualisering og moderne DevOps-praksis.

### Brukte teknologier

- **Maskinvare:** Dell (gammel PC)
- **Hypervisor:** Proxmox VE
- **Operativsystem:** Ubuntu Server LTS
- **Webserver:** Nginx
- **Versjonskontrollsystem:** Git
- **Plattform for kodelagring:** GitHub

---

## Systemarkitektur

```
┌─────────────────────────────────────┐
│   Fysisk server (Dell)              │
│   ┌─────────────────────────────┐   │
│   │   Proxmox VE                │   │
│   │   ┌─────────────────────┐   │   │
│   │   │  Ubuntu Server VM   │   │   │
│   │   │  ┌───────────────┐  │   │   │
│   │   │  │    Nginx      │  │   │   │
│   │   │  │  Web Server   │  │   │   │
│   │   │  └───────────────┘  │   │   │
│   │   └─────────────────────┘   │   │
│   └─────────────────────────────┘   │
└─────────────────────────────────────┘
```

---

## Konfigurasjon av virtuell maskin

### 1. Installasjon av Proxmox VE

**Steg 1:** Forberedelse av fysisk server

- Brukte Dell-datamaskin som fysisk vert
- Sikret tilkobling til nettverk

**Steg 2:** Installasjon av Proxmox

- Lastet ned ISO-bilde av Proxmox VE fra offisiell nettside
- Opprettet oppstartbar USB-enhet
- Utførte ren installasjon av Proxmox på Dell-serveren
- Konfigurerte nettverksparametere

### 2. Opprettelse av Ubuntu Server VM

**Parametere for virtuell maskin:**

```
VM-navn: Ubuntu-Web-Server
OS: Ubuntu Server LTS (22.04 eller nyere)
CPU: 2 kjerner
RAM: 2048 MB
Disk: 20 GB
Nettverk: Bridged (tilgang til lokalt nettverk)
```

**Opprettingssteg:**

1. I Proxmox webgrensesnitt, trykk "Create VM"
2. Velg ISO-bilde av Ubuntu Server
3. Konfigurer ressurser i henhold til parametrene ovenfor
4. Start VM og gjennomfør installasjonen av Ubuntu Server
5. Under installasjonen, velg minimal konfigurasjon
6. Opprett bruker og sett passord

**Etter installasjon:**

```bash
# Oppdatering av system
sudo apt update
sudo apt upgrade -y

# Installasjon av nødvendige verktøy
sudo apt install -y curl wget net-tools
```

---

## Installasjon og konfigurasjon av webserver

### Installasjon av Nginx

```bash
# Installasjon av Nginx
sudo apt install nginx -y

# Start og legg til i autostart
sudo systemctl start nginx
sudo systemctl enable nginx

# Sjekk status
sudo systemctl status nginx
```

### Konfigurasjon av brannmur

```bash
# Tillat HTTP-trafikk
sudo ufw allow 'Nginx HTTP'

# Aktiver UFW
sudo ufw enable

# Sjekk brannmurstatus
sudo ufw status
```

### Nginx-mappestruktur

```
/etc/nginx/
├── nginx.conf              # Hovedkonfigurasjonsfil
├── sites-available/        # Tilgjengelige sidekonfigurasjoner
└── sites-enabled/          # Aktive konfigurasjoner

/var/www/html/              # Rotmappe for webinnhold
└── index.html              # Hovedside
```

---

## Utvikling av velkomstside

### Opprettelse av HTML-fil

På hovedarbeidsdatamaskinen ble filen `index.html` opprettet:

```html
<!DOCTYPE html>
<html lang="no">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Server Status - Welcome</title>
    <link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@700&family=Source+Sans+3:wght@300;400;600&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary: #1a4d2e;
            --accent: #4f7942;
            --light: #f0f4f1;
            --text: #2d3e36;
            --shadow: rgba(26, 77, 46, 0.12);
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Source Sans 3', sans-serif;
            background: linear-gradient(135deg, var(--light) 0%, #e8f5e9 100%);
            color: var(--text);
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 2rem;
            position: relative;
            overflow-x: hidden;
        }

        body::before {
            content: '';
            position: absolute;
            top: -50%;
            right: -20%;
            width: 70%;
            height: 150%;
            background: radial-gradient(circle, rgba(79, 121, 66, 0.08) 0%, transparent 70%);
            border-radius: 50%;
            animation: float 20s ease-in-out infinite;
        }

        @keyframes float {
            0%, 100% { transform: translate(0, 0) scale(1); }
            50% { transform: translate(-30px, 30px) scale(1.05); }
        }

        @keyframes slideUp {
            from {
                opacity: 0;
                transform: translateY(30px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        @keyframes pulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.6; }
        }

        .container {
            max-width: 800px;
            width: 100%;
            background: white;
            border-radius: 24px;
            box-shadow: 0 20px 60px var(--shadow);
            padding: 4rem;
            position: relative;
            animation: slideUp 0.8s ease-out;
            border: 1px solid rgba(79, 121, 66, 0.1);
        }

        .status-badge {
            display: inline-flex;
            align-items: center;
            gap: 0.5rem;
            background: linear-gradient(135deg, var(--primary), var(--accent));
            color: white;
            padding: 0.75rem 1.5rem;
            border-radius: 50px;
            font-size: 0.9rem;
            font-weight: 600;
            margin-bottom: 2rem;
            animation: slideUp 0.8s ease-out 0.2s backwards;
        }

        .status-dot {
            width: 10px;
            height: 10px;
            background: #4ade80;
            border-radius: 50%;
            animation: pulse 2s ease-in-out infinite;
            box-shadow: 0 0 10px rgba(74, 222, 128, 0.5);
        }

        h1 {
            font-family: 'Playfair Display', serif;
            font-size: 3rem;
            color: var(--primary);
            margin-bottom: 1rem;
            line-height: 1.2;
            animation: slideUp 0.8s ease-out 0.3s backwards;
        }

        .subtitle {
            font-size: 1.25rem;
            color: var(--accent);
            margin-bottom: 3rem;
            font-weight: 300;
            animation: slideUp 0.8s ease-out 0.4s backwards;
        }

        .info-grid {
            display: grid;
            gap: 1.5rem;
            margin-bottom: 3rem;
        }

        .info-card {
            background: linear-gradient(135deg, var(--light), #ffffff);
            padding: 1.5rem;
            border-radius: 16px;
            border-left: 4px solid var(--accent);
            animation: slideUp 0.8s ease-out calc(0.5s + var(--delay)) backwards;
            transition: transform 0.3s ease, box-shadow 0.3s ease;
        }

        .info-card:hover {
            transform: translateX(8px);
            box-shadow: 0 4px 20px var(--shadow);
        }

        .info-label {
            font-size: 0.85rem;
            text-transform: uppercase;
            letter-spacing: 1px;
            color: var(--accent);
            font-weight: 600;
            margin-bottom: 0.5rem;
        }

        .info-value {
            font-size: 1.1rem;
            color: var(--text);
            font-weight: 400;
            line-height: 1.6;
        }

        .info-value.mono {
            font-family: 'Courier New', monospace;
            background: rgba(26, 77, 46, 0.05);
            padding: 0.5rem 1rem;
            border-radius: 8px;
            display: inline-block;
        }

        .project-section {
            background: linear-gradient(135deg, var(--primary), var(--accent));
            color: white;
            padding: 2rem;
            border-radius: 16px;
            margin-top: 2rem;
            animation: slideUp 0.8s ease-out 0.9s backwards;
        }

        .project-section h2 {
            font-family: 'Playfair Display', serif;
            font-size: 1.5rem;
            margin-bottom: 1rem;
        }

        .project-section p {
            line-height: 1.8;
            opacity: 0.95;
        }

        footer {
            text-align: center;
            margin-top: 2rem;
            font-size: 0.9rem;
            color: var(--accent);
            animation: slideUp 0.8s ease-out 1s backwards;
        }

        .decorative-line {
            height: 2px;
            background: linear-gradient(90deg, transparent, var(--accent), transparent);
            margin: 2rem 0;
            opacity: 0.3;
        }

        @media (max-width: 640px) {
            .container {
                padding: 2rem;
            }

            h1 {
                font-size: 2rem;
            }

            .subtitle {
                font-size: 1rem;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="status-badge">
            <span class="status-dot"></span>
            Server aktiv
        </div>

        <h1>Velkommen!</h1>
        <p class="subtitle">Server vellykket rullet ut og klar til bruk</p>

        <div class="info-grid">
            <div class="info-card" style="--delay: 0s">
                <div class="info-label">Administrator</div>
                <div class="info-value">[Ditt navn]</div>
            </div>

            <div class="info-card" style="--delay: 0.1s">
                <div class="info-label">VM IP-adresse</div>
                <div class="info-value mono">192.168.20.28</div>
            </div>
        </div>

        <div class="decorative-line"></div>

        <div class="project-section">
            <h2>Om prosjektet</h2>
            <p>
                Denne serveren er opprettet som en del av et utdanningsprosjekt for å lære 
                DevOps-praksis og Linux-systemadministrasjon. På en virtuell maskin med 
                Ubuntu Server er det rullet ut en Nginx webserver som brukes til hosting 
                av webapplikasjoner.
            </p>
            <p style="margin-top: 1rem;">
                I fremtiden planlegges utvidelse av funksjonalitet ved å legge til databaser, 
                CI/CD-pipelines, containerisering med Docker, samt implementering av 
                overvåking og logging for å sikre pålitelig drift av tjenestene.
            </p>
        </div>

        <footer>
            <p>Server uptime: <span id="uptime">Beregner...</span></p>
        </footer>
    </div>

    <script>
        // Enkel uptime-teller (fra sidens lasting)
        const startTime = Date.now();
        function updateUptime() {
            const elapsed = Date.now() - startTime;
            const seconds = Math.floor(elapsed / 1000);
            const minutes = Math.floor(seconds / 60);
            const hours = Math.floor(minutes / 60);
            const displayHours = hours;
            const displayMinutes = minutes % 60;
            const displaySeconds = seconds % 60;
            document.getElementById('uptime').textContent =
                `${displayHours.toString().padStart(2, '0')}:${displayMinutes.toString().padStart(2, '0')}:${displaySeconds.toString().padStart(2, '0')}`;
        }
        updateUptime();
        setInterval(updateUptime, 1000);
    </script>
</body>
</html>
```

### Henting av VM IP-adresse

```bash
# På Ubuntu Server, kjør kommando
ip addr show

# Eller kortere versjon
hostname -I
```

---

## Arbeid med Git og GitHub

### 1. Initialisering av Git-repository på arbeidsdatamaskin

```bash

# Initialisering av Git
git init

# Legge til filer i Git
git add .

# Første commit
git commit -m "Initial commit: Add welcome page"
```

### 2. Opprettelse av repository på GitHub

1. Gå til github.com og logg inn på kontoen
2. Trykk "New repository"
3. Navngi repository, for eksempel: `Felles-del`
4. Legg til prosjektbeskrivelse
5. La repository være offentlig
6. Ikke legg til README, .gitignore, license (allerede opprettet lokalt)

### 3. Kobling av lokalt repository til GitHub

```bash
# Legge til eksternt repository
git remote add origin https://github.com/kof1k/Felles-del

# Verifisering
git remote -v

# Sende kode til GitHub
git branch -M main
git push -u origin main
```

### 4. Kloning av repository til server

```bash
# På Ubuntu Server
cd /home/[din-bruker]

# Installasjon av Git (hvis ikke installert)
sudo apt install git -y

# Kloning av repository
git clone https://github.com/kof1k/Felles-del

# Kopiering av filer til Nginx-mappe
sudo cp /var/www/Felles-del/ * /var/www/html/

# Gi riktige tilgangsrettigheter
sudo chown www-data:www-data /var/www/html/index.html
sudo chmod 644 /var/www/html/index.html
```

### 5. Restart av Nginx

```bash
# Sjekk konfigurasjon
sudo nginx -t

# Restart server
sudo systemctl restart nginx
```

### 6. Arbeidsprosess med Git

```bash
# Ved endringer på arbeidsdatamaskin:
git add .
git commit -m "Beskrivelse av endringer"
git push origin main

# På server for oppdatering:
cd ~/ubuntu-web-server
git pull origin main
sudo cp index.html /var/www/html/
sudo systemctl reload nginx
```

---

## Konfigurasjon av lokalt DNS

For å gjøre webserveren tilgjengelig via et domenenavn i stedet for IP-adresse, kan vi konfigurere lokal DNS ved hjelp av hosts-filen.

### Konfigurasjon på Windows

#### Steg 1: Åpne hosts-filen

Hosts-filen på Windows ligger i: `C:\Windows\System32\drivers\etc\hosts`

**Metode 1: Via Notepad**

1. Åpne **PowerShell** eller **Command Prompt** som administrator
2. Kjør kommandoen:

```powershell
notepad C:\Windows\System32\drivers\etc\hosts
```

**Metode 2: Via PowerShell-kommandoer**

```powershell
# Åpne PowerShell som administrator
# Legg til DNS-oppføringer
Add-Content -Path C:\Windows\System32\drivers\etc\hosts -Value "`n# Lokal webserver"
Add-Content -Path C:\Windows\System32\drivers\etc\hosts -Value "192.168.20.28    mywebserver.local"
Add-Content -Path C:\Windows\System32\drivers\etc\hosts -Value "192.168.20.28    www.mywebserver.local"
```

#### Steg 2: Legg til DNS-oppføringer

Legg til følgende linjer i slutten av hosts-filen:

```
# Lokal webserver
192.168.20.28    mywebserver.local
192.168.20.28    www.mywebserver.local
```

Lagre filen (`Ctrl+S`).

#### Steg 3: Tøm DNS-cache

```powershell
# I PowerShell som administrator
ipconfig /flushdns

# Output skal vise:
# Successfully flushed the DNS Resolver Cache.
```

#### Steg 4: Verifiser DNS-konfigurasjon

```powershell
# Test DNS-oppløsning
ping mywebserver.local

# Output skal vise:
# Pinging mywebserver.local [192.168.20.28] with 32 bytes of data:
# Reply from 192.168.20.28: bytes=32 time<1ms TTL=64
```

### Konfigurasjon av Nginx virtuell vert

På serveren må vi konfigurere Nginx til å svare på domenenavnet.

#### Steg 1: Opprett konfigurasjonsfil

```bash
# På Ubuntu Server
sudo nano /etc/nginx/sites-available/mywebserver.local
```

#### Steg 2: Legg til serverkonfigurasjon

```nginx
server {
    listen 80;
    listen [::]:80;

    # Domenenavn som serveren skal svare på
    server_name mywebserver.local www.mywebserver.local;

    # Rotmappe for nettstedet
    root /var/www/html;
    index index.html index.htm;

    # Loggfiler
    access_log /var/log/nginx/mywebserver.access.log;
    error_log /var/log/nginx/mywebserver.error.log;

    # Hovedlokasjon
    location / {
        try_files $uri $uri/ =404;
    }

    # Sikkerhetshoder
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Referrer-Policy "no-referrer-when-downgrade" always;

    # Skjul Nginx-versjon
    server_tokens off;

    # Deaktiver unødvendige HTTP-metoder
    if ($request_method !~ ^(GET|HEAD|POST)$ ) {
        return 405;
    }
}
```

Lagre filen: `Ctrl+O`, `Enter`, `Ctrl+X`

#### Steg 3: Aktiver konfigurasjonen

```bash
# Opprett symbolsk lenke
sudo ln -s /etc/nginx/sites-available/mywebserver.local /etc/nginx/sites-enabled/

# Test konfigurasjon for syntaksfeil
sudo nginx -t

# Output skal vise:
# nginx: configuration file /etc/nginx/nginx.conf test is successful

# Last inn ny konfigurasjon
sudo systemctl reload nginx

# Sjekk status
sudo systemctl status nginx
```

### Testing av DNS-konfigurasjon

#### Fra Windows

**Test 1: Ping**
```powershell
ping mywebserver.local
```

**Test 2: HTTP-forespørsel**
```powershell
curl http://mywebserver.local
```

**Test 3: Åpne i nettleser**
```powershell
# Åpne automatisk i standard nettleser
start http://mywebserver.local
```

#### Manuell test i nettleser

Åpne nettleseren og naviger til:
- `http://mywebserver.local`
- `http://www.mywebserver.local`
- `http://192.168.20.28` (skal også fungere)

Alle tre adressene skal vise velkomstsiden.

### Oppdatering av velkomstside (valgfritt)

For å vise domenenavnet på velkomstsiden:

```bash
sudo nano /var/www/html/index.html
```



### Feilsøking

**Problem: Kan ikke nå domenenavnet**

1. Sjekk hosts-filen:
```powershell
type C:\Windows\System32\drivers\etc\hosts
```

2. Verifiser at oppføringen eksisterer
3. Tøm DNS-cache på nytt:
```powershell
ipconfig /flushdns
```

**Problem: 404 Not Found**

1. Sjekk Nginx-konfigurasjon:
```bash
sudo nginx -t
```

2. Sjekk Nginx-logger:
```bash
sudo tail -f /var/log/nginx/error.log
```

3. Verifiser at filen eksisterer:
```bash
ls -la /var/www/html/index.html
```

**Problem: Tilkoblingsnekt**

1. Sjekk at Nginx kjører:
```bash
sudo systemctl status nginx
```

2. Sjekk brannmuren:
```bash
sudo ufw status
```

3. Verifiser at port 80 er åpen:
```bash
sudo netstat -tulpn | grep :80
```

---

## Refleksjon om IT-sikkerhet

### Nåværende sikkerhetsnivå

**Sterke sider:**

- Bruk av LTS-versjon av Ubuntu sikrer stabilitet og sikkerhetsoppdateringer
- Nginx er en pålitelig og sikker webserver med aktivt community
- Virtualisering gjennom Proxmox skaper et ekstra isoleringslag
- Bruk av Git sikrer versjonskontroll og mulighet for tilbakerulling

**Svake sider:**

- Manglende HTTPS/SSL-sertifikater (trafikk overføres i klartekst)
- Grunnleggende brannmurkonfigurasjon (kun HTTP tillatt)
- Manglende overvåkings- og loggingssystem
- Ingen konfigurert automatisk sikkerhetskopiering
- Manglende beskyttelse mot DDoS-angrep
- Ingen begrensning på forespørselshastighet (rate limiting)

### Sikkerhetsprinsipper jeg følger

1. **Prinsippet om minste privilegium:** Bruk av egen bruker for webserver (www-data)
2. **Systemoppdateringer:** Regelmessig oppdatering av pakker for å lukke sårbarheter
3. **Versjonskontroll:** Bruk av Git for å spore alle endringer
4. **Isolasjon:** Kjøring av server i virtuell maskin for isolasjon fra vertssystem


#### Langsiktige forbedringer:

1. **Implementering av overvåking:**
   - Installasjon av Prometheus + Grafana for overvåking av metrikker
   - Konfigurasjon av Loki eller ELK Stack for sentralisert logging
   - Varsler om mistenkelig aktivitet

2. **Sikkerhetskopiering:**
   - Automatisk sikkerhetskopiering av VM gjennom Proxmox
   - Sikkerhetskopiering av konfigurasjonsfiler i Git
   - Testing av gjenopprettingsprosess

3. **Containerisering:**
   - Migrering til Docker for bedre isolasjon
   - Bruk av Docker Compose for administrasjon
   - Skanning av images for sårbarheter

4. **Implementering av CI/CD:**
   - GitHub Actions for automatisk testing
   - Automatisk deployment etter vellykkede tester
   - Automatisk skanning av kode for sårbarheter

5. **Nettverkssikkerhet:**
   - Implementering av VPN for ekstern tilgang
   - Nettverkssegmentering (DMZ for webserver)
   - Implementering av IDS/IPS-system (Snort/Suricata)

6. **Tilgangsstyring:**
   - Bruk av SSH-nøkler i stedet for passord
   - Deaktivering av root-tilgang via SSH
   - Implementering av tofaktorautentisering

---

## Bruk av AI i læring

### Hvordan jeg brukte AI (Claude AI)

**1. Generering og strukturering av dokumentasjon:**

- Jeg ga AI en kort beskrivelse av det jeg hadde gjort (på ukrainsk)
- AI strukturerte denne informasjonen til profesjonell dokumentasjon
- La til manglende detaljer og utvidet forklaringer

**2. Hjelp til å forstå kommandoer:**

- AI forklarte formålet med forskjellige Linux-kommandoer
- Ga eksempler på bruk med kommentarer
- Viste alternative tilnærminger til problemløsning

**3. Opprettelse av HTML/CSS-kode:**

- AI opprettet profesjonell velkomstside med moderne design
- Koden var fullstendig funksjonell og oppfylte kravene

**4. Sikkerhetsanalyse:**

- AI hjalp med å identifisere svakheter i sikkerheten
- Ga konkrete anbefalinger for forbedring
- Viste eksempler på konfigurasjoner for beskyttelse

### Fordeler med å bruke AI

 **Hastighet:** Dokumentasjon opprettet på minutter
 **Strukturering:** Klar organisering av informasjon
 **Fullstendighet:** AI la til detaljer jeg ikke tenkte på
 **Læring:** Lærte om nye konsepter og praksis
 **Profesjonalitet:** Resultatet ser ut som profesjonell dokumentasjon

### Begrensninger og advarsler

 **Behov for verifisering:** Må sjekke genererte kommandoer før utførelse
 **Spesifisitet:** AI kjenner ikke mine spesifikke IP-adresser og parametere
 **Forståelse:** Viktig å forstå hva koden gjør, ikke bare kopiere
 **Kontekst:** AI kan ikke vite om spesifikke begrensninger i ditt miljø

### Kritisk tenkning

**Hva jeg gjorde selv, og hva AI gjorde:**

**Selvstendig:**

- Installasjon og konfigurasjon av Proxmox
- Opprettelse og konfigurasjon av VM
- Installasjon av Nginx
- Arbeid med Git og GitHub
- Kopiering av filer til server
- Testing av serverfunksjonalitet

**Med hjelp fra AI:**

- Strukturering av dokumentasjon
- Opprettelse av HTML-kode for velkomstside
- Anbefalinger om sikkerhet
- Forklaring av komplekse konsepter
- Eksempler på kommandoer og konfigurasjoner

### Konklusjon om bruk av AI

AI er et kraftig verktøy for læring, men ikke en erstatning for forståelse. Best resultat oppnås når:

1. Du utfører praktisk arbeid selvstendig
2. Du bruker AI til forklaringer og dokumentering
3. Du kritisk vurderer anbefalinger fra AI
4. Du verifiserer og tester alt før implementering

**AI er en assistent, ikke en erstatning for læring. Forståelse av grunnleggende prinsipper forblir kritisk viktig.**

---

### Prosjektstruktur

```
Felles-del/
├── index.html              # Velkomstside
└── README.md               # Denne dokumentasjonen
```

### Kontakter og ressurser

- **GitHub repository:** https://github.com/[ditt-brukernavn]/ubuntu-web-server
- **Offisiell Nginx-dokumentasjon:** https://nginx.org/en/docs/
- **Ubuntu Server-dokumentasjon:** https://ubuntu.com/server/docs
- **Proxmox-dokumentasjon:** https://pve.proxmox.com/wiki/Main_Page

---

**Opprettelsesdato:** 06.01.2026  
**Forfatter:** Kostiatyn Chyzh

