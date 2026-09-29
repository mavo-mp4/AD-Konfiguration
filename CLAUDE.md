# Übergabe: Prüfung der AD-Dokumentation (Lernfeld 10)

## Aufgabe
Die Dokumentation `Lernfeld 10 - Dokumentation AD.docx` wird mit der Aufgabenstellung
`ADS-Konfiguration RWE mit Domäne und Skripten.doc` abgeglichen und korrigiert bzw. ergänzt.

**Vorgabe:** Die Doku bezieht sich NUR auf die Aufgaben aus „ADS-Konfiguration“.
Wie man dorthin kommt (z. B. AD-DS installieren, DC heraufstufen), Tests und Nachweise sowie
weitere Aufgaben gehören nicht hinein.

Umgebung laut Doku: Server `KLAUSUR`, Domäne `rwe01.local` (Aufgabe: rweXX.local), Windows-10-Client.

## Ergebnis der Prüfung

| Aufgabe | Stand in der Doku |
|---|---|
| OU RWE + 5 Unter-OUs | erfüllt (Schritt 3 und 10 überschneiden sich) |
| Konten in allen OUs | nur Ausbildungswerkstatt; IT, Personal, Forschung und Fertigung fehlen |
| Kennwortänderung bei 1. Anmeldung | `-mustchpwd yes` drin, wird aber durch `-pwdneverexpires yes` aufgehoben |
| Serverbasierte Profile (alle außer Fertigung) | fehlt (Profile$-Freigabe, `-profile`) |
| HOME Z: → C:\Home, Home$ | im Wesentlichen erfüllt |
| IT: Software.cmd → T: → X:\Software | falsch: Skript heißt Software.bat, verbindet U: und zeigt auf Ersatzteile |
| Fertigung: Ersatzteile.bat → U: → X:\Ersatzteile | Inhalt steht im falschen Skript; der Ordner Ersatzteile fehlt in Schritt 9 (Satz bricht ab) |
| Skript nur bei IT bzw. Fertigung | `-loscr Software.bat` steht in der Azubi-Schleife |
| Profil max. 500 MB | Einstellung genannt; Wert (500000 KB) und GPO-Verknüpfung fehlen |
| Fertigung nicht herunterfahren | Einstellung richtig; die GPO muss NUR mit der OU Fertigung verknüpft sein (nicht Default Domain Policy) |
| Chrome für IT / Firefox für Fertigung | fehlt (GPO-Softwareinstallation, Zugewiesen, „Bei Anmeldung installieren“, UNC-Pfad \\KLAUSUR\Pakete$) |
| www.rwe.de, www.ewr.de, ww.rwe.de | fehlt (Forward-Lookupzonen rwe.de und ewr.de, A-Einträge) |
| Reverse-Lookup IP → Servername | fehlt (Reverse-Lookupzone + PTR-Eintrag für KLAUSUR) |
| Client tritt der Domäne bei | fehlt (sysdm.cpl → Ändern → Domäne) |

Schritte 1, 2 und 8 (IP prüfen, DNS am Client, Laufwerke) sind eigentlich Vorbereitung.
Die Kennwortrichtlinie (Schritte 4–6) bleibt drin, weil sonst kurze Kennwörter wie az01 abgelehnt werden.

## Wichtige Hinweise
- `-pwdneverexpires yes` entfernen: „Kennwort läuft nie ab“ hat Vorrang vor „Kennwort bei nächster
  Anmeldung ändern“. Active Directory-Benutzer und -Computer warnt beim Setzen beider Haken genau davor.
- Sachbearbeiter10 braucht eine eigene Zeile (sonst entsteht `Sachbearbeiter010`); Forscher11–14 und
  Fertigung15–20 ohne führende 0.
- `dsadd user` kennt `-mustchpwd`, `-hmdir`, `-hmdrv`, `-profile` und `-loscr` direkt; dsmod ist überflüssig.
- `-hmdir` legt die Home-Ordner nicht an → `md` + `icacls` ergänzen.
- In .bat-Dateien `%%i` statt `%i` verwenden.
- Parameter je OU: Ausbildungswerkstatt, Personal, Forschung = `-profile`; IT = `-profile` + `-loscr Software.cmd`;
  Fertigung = kein `-profile`, `-loscr Ersatzteile.bat`.
- Kennwörter: az/ita/sb/fo/fe + Benutzernummer (az01 …). Offene Frage: Bedeutet „xx“ die Benutzernummer
  oder die Platznummer? Bei der Lehrkraft klären.

## Nächster Schritt
Eine korrigierte und erweiterte Fassung als neue Word-Datei erstellen
(z. B. `Lernfeld 10 - Dokumentation AD (überarbeitet).docx`). Das Original bleibt unverändert.
Hinweis: Die .doc-Aufgabenstellung ist ein altes Word-Format und muss zum Lesen z. B. mit LibreOffice
oder Word in .docx konvertiert werden.
