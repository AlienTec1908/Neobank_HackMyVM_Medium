# Neobank - HackMyVM (Medium)

![Neobank.png](Neobank.png)

## Übersicht

*   **VM:** Neobank
*   **Plattform:** HackMyVM (https://hackmyvm.eu/machines/machine.php?vm=Neobank)
*   **Schwierigkeit:** Medium
*   **Autor der VM:** DarkSpirit
*   **Datum des Writeups:** 19. November 2022
*   **Original-Writeup:** https://alientec1908.github.io/Neobank_HackMyVM_Medium/
*   **Autor:** Ben C.

## Kurzbeschreibung

Das Ziel dieser Challenge war es, die User- und Root-Flags der Maschine "Neobank" zu erlangen. Der Angriffsvektor war eine Webanwendung, die auf Port 5000 lief (Werkzeug httpd / Python). Durch Enumeration wurde ein öffentlich zugänglicher Endpunkt (`/email_list`) entdeckt, der eine Liste gültiger E-Mail-Adressen (Benutzernamen) preisgab. Anschließend wurde ein benutzerdefiniertes Python-Skript verwendet, um einen Brute-Force-Angriff auf die Login-Seite (`/login`) durchzuführen, wobei die gefundenen E-Mails mit einer Liste gängiger PINs kombiniert wurden. Dies führte zur Entdeckung gültiger Zugangsdaten (z.B. `zeus@neobank.vln:123456`). Der Bericht endet nach diesem erfolgreichen Login-Brute-Force; die weiteren Schritte zur Erlangung der Flags wurden nicht detailliert.

## Disclaimer / Wichtiger Hinweis

Die in diesem Writeup beschriebenen Techniken und Werkzeuge dienen ausschließlich zu Bildungszwecken im Rahmen von legalen Capture-The-Flag (CTF)-Wettbewerben und Penetrationstests auf Systemen, für die eine ausdrückliche Genehmigung vorliegt. Die Anwendung dieser Methoden auf Systeme ohne Erlaubnis ist illegal. Der Autor übernimmt keine Verantwortung für missbräuchliche Verwendung der hier geteilten Informationen. Handeln Sie stets ethisch und verantwortungsbewusst.

## Verwendete Tools

*   `arp-scan`
*   `nmap`
*   `gobuster`
*   `nikto`
*   `wget`
*   Python3 (`requests`, `threading` Module für Brute-Force-Skript)
*   Standard Linux-Befehle (`cat`)

## Lösungsweg (Zusammenfassung)

Der Angriff auf die Maschine "Neobank" gliederte sich in folgende Phasen:

1.  **Reconnaissance & Web Enumeration:**
    *   IP-Adresse des Ziels (192.168.2.126) mit `arp-scan` identifiziert.
    *   `nmap`-Scan offenbarte nur Port 5000 (HTTP, Werkzeug httpd 1.0.1 / Python 3.7.3) mit dem Titel "Login".
    *   `gobuster` auf `http://192.168.2.126:5000/` fand die Endpunkte `/login`, `/logout`, `/otp`, `/qr` und `/withdraw`.
    *   `nikto` meldete fehlende Sicherheitsheader.
    *   Eine weitere `gobuster`-Suche mit einer benutzerdefinierten Wortliste (`email.txt`) fand den Endpunkt `/email_list`.
    *   Mittels `wget http://192.168.2.126:5000/email_list -O emails.txt` wurde eine Liste von E-Mail-Adressen heruntergeladen, die als Benutzernamen dienten.

2.  **Login Bruteforce (Initial Access):**
    *   Ein benutzerdefiniertes Python-Skript (`otpHack.py`) wurde verwendet, um einen Brute-Force-Angriff auf den `/login`-Endpunkt durchzuführen.
    *   Das Skript kombinierte die E-Mail-Adressen aus `emails.txt` mit einer Liste potenzieller PINs aus `pins.txt`.
    *   Der Angriff war erfolgreich und fand mindestens eine gültige Kombination: `zeus@neobank.vln` mit der PIN `123456`.
    *   *Der Bericht dokumentiert nicht die Schritte nach diesem erfolgreichen Login oder wie die Flags erlangt wurden.*

3.  **Flags (exfiltriert auf nicht gezeigte Weise):**
    *   User-Flag: `7766554433221223344556677`
    *   Root-Flag: `1123581321355691`

## Wichtige Schwachstellen und Konzepte

*   **Information Disclosure (Benutzernamen-Leak):** Ein öffentlich zugänglicher Endpunkt (`/email_list`) gab eine Liste gültiger E-Mail-Adressen/Benutzernamen preis.
*   **Fehlender Brute-Force-Schutz:** Die Login-Seite war anfällig für Brute-Force-Angriffe, da keine Mechanismen wie Rate Limiting oder Account Lockout implementiert zu sein schienen.
*   **Verwendung schwacher PINs/Passwörter:** Die erfolgreiche Kompromittierung durch Brute-Force mit einer gängigen PIN (`123456`) deutet auf die Verwendung schwacher Anmeldeinformationen hin.
*   **Fehlende Sicherheitsheader:** Nikto meldete das Fehlen wichtiger HTTP-Sicherheitsheader.

## Flags

*   **User Flag:** `7766554433221223344556677`
*   **Root Flag:** `1123581321355691`

## Tags

`HackMyVM`, `Neobank`, `Medium`, `Information Disclosure`, `Login Brute-Force`, `Python`, `Werkzeug`, `Linux`, `Web`
