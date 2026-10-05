# Übergabe: Prüfung der AD-Dokumentation (Lernfeld 10)

## Aufgabe
Die Dokumentation `Lernfeld 10 - Dokumentation AD.docx` wird mit der Aufgabenstellung
`ADS-Konfiguration RWE mit Domäne und Skripten.doc` abgeglichen und korrigiert bzw. ergänzt.

**Vorgabe:** Die Doku bezieht sich NUR auf die Aufgaben aus „ADS-Konfiguration“.
Wie man dorthin kommt (z. B. AD-DS installieren, DC heraufstufen), Tests und Nachweise sowie
weitere Aufgaben gehören nicht hinein.

Umgebung laut Doku (seit 05.10.2026 an die Lab-VM angepasst): Server `KSERVER`, Domäne `klausur.local`
(`DC=klausur,DC=local`, NetBIOS `KA`), Windows-10-Client. Aufgabe verlangt eigentlich rweXX.local.
Vorherige Stände gesichert: „… - Sicherung vor KServer.docx“ (KLAUSUR/rwe01.local), „… - Sicherung vor klausur.local.docx“.
Der Prüfbericht „Lernfeld 10 - AD Prüfung und effizientere Methoden.docx“ nutzt noch KLAUSUR/rwe01.local.
Echte Lab-VM des Nutzers (Export 05.10.2026): Server KSERVER, Domäne klausur.local (NetBIOS KA), IP 192.168.1.99,
Software/Ersatzteile liegen auf C:\ (kein X:). Client ist Win11-Client1 (zeitweise Home-Edition → kein Domänenbeitritt).
Fehler in der VM laut Export: UNC-Pfade \\KLAUSUR statt \\KSERVER, Fertigung mit Profilpfad, keine ScriptPaths,
Konto „Sachbearbeiter%i“ statt Sachbearbeiter10, Software.bat statt .cmd, Ersatzteile.bat verbindet T: statt U:,
DNS ww.rwe.de fehlt. Korrektur-Befehle wurden im Chat gegeben.

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
- Kennwörter: az/ita/sb/fo/fe + Benutzernummer (az01 …). Geklärt: „xx“ = Benutzernummer.

## Vorgaben des Nutzers zur Form
- Keine Schritt-für-Schritt-Anleitung, sondern knapp.
- Wiederholende Teile verkürzen: Kontenskript nur am Beispiel Ausbildungswerkstatt,
  die anderen OUs als Tabelle mit den Abweichungen.

## Stand (29.09.2026)
`Lernfeld 10 - Dokumentation AD (überarbeitet).docx` ist erstellt (Original unverändert).
Sie enthält alle Aufgaben: OUs, Kennwortrichtlinie, Freigaben, Anmeldeskripte, Konten, GPOs
(Profilgröße 512000 KB an RWE, Chrome an IT, Firefox + Herunterfahren an Fertigung), DNS-Zonen,
Reverse-Lookup und den Domänenbeitritt.
Erzeugt mit python-docx (Skript lag im Scratchpad, nicht im Repo). Auf diesem PC gibt es weder Word
noch LibreOffice, deshalb ist das Layout nicht optisch geprüft; die XML-Validierung ist bestanden.

## Abgleich mit Rudi_Dokumentation_ADS.pdf (05.10.2026)
Rudis Doku (Domäne klausur.local, Server KServer) deckt sich im Wesentlichen; keine Aufgabe fehlte.
Übernommen wurden nur sinnvolle Ergänzungen: Hinweis auf `dsquery domainroot` und Reihenfolge der OUs,
Löschschutz entfernen, Kennwortchronik 0 und maximales Kennwortalter 0, Anmeldeskripte alternativ per GPO,
Profil-/Home-Pfad alternativ per Mehrfachauswahl mit %username%, Schlüsselwortfilter, 500000 KB als Alternative.
Nicht übernommen: VM-/Netzwerkvorbereitung, Tests (gpresult, nslookup), EXE-Verteilung per PowerShell,
SG_IT-Computer und die Verknüpfung der IT-GPO an die Domäne, putty, DNS-Zone „de“.
Sicherung vor der Änderung: `Lernfeld 10 - Dokumentation AD (überarbeitet) - Sicherung 2026-10-05.docx`.

## Internet-Prüfung (05.10.2026)
`Lernfeld 10 - AD Prüfung und effizientere Methoden.docx` enthält: Vollständigkeitstabelle, Korrekturen,
Methodenvergleich, PowerShell-Variante (Konten aller OUs aus einer Tabelle, GPOs, DNS, Add-Computer), Quellen.
Ergebnis: alles abgedeckt. In die Doku noch NICHT übernommen:
- Name korrigieren: „Profilgröße beschränken“; Option „Benutzer beim Überschreiten der max. Profilspeichergröße benachrichtigen“
- Hinweis: Softwareinstallation evtl. erst bei 2. Anmeldung („Beim Neustart … immer auf das Netzwerk warten“)
- optional: Home-Ordner-Rechte (icacls mit SIDs), Chrome/Firefox-MSI installieren pro Computer

Hinweis: Die .doc-Aufgabenstellung lässt sich mit `antiword -m UTF-8.txt <datei>` (Git Bash) lesen.
