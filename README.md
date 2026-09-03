# Keys 4 IT Solutions — Kontor

**Keys 4 IT Solutions** ist die Projektbezeichnung, unter der diese Arbeit
auftritt. **Kontor** ist die Plattform, die darunter entsteht.

Dieses Repository ist das Schaufenster: es beschreibt, was Kontor ist und was
es kann, und enthält die drei Rechtsseiten. **Der Quelltext der Anwendung
liegt in einem privaten Repository und ist nicht Teil dieser Ablage.** Hier
steht kein Anwendungscode, keine Konfiguration und nichts zum Betrieb.

---

## Was Kontor ist

Eine **chart-zentrierte Oberfläche für die Beobachtung und Analyse von
Finanzmärkten**, mit persönlichem Konto.

Das Kursbild ist das Kernstück, nicht ein Beiwerk. Chart und Zeichenwerkzeuge
kommen von der **TradingView Charting Library**, lokal ausgeliefert, mit den
Zeitrahmen M1 bis MN. Kurse und Instrumentendaten liefert die **Broker-API**
über einen eigenen UDF-Datafeed; angebunden ist heute Saxo. Rechts daneben
stehen abschaltbare Analysemodule und ein **Agent**, der Fragen zur Marktlage
in Textform beantwortet.

Die Broker-Anbindung ist im Normalzustand **ausschliesslich lesend**:
Handelsfunktionen sind per Vorgabe abgeschaltet, und der Betrieb läuft gegen
ein Demo-Konto (SIM).

---

## Was Kontor kann

### Chart

- TradingView Charting Library, lokal ausgeliefert — native Zeichenwerkzeuge
  und Indikatoren, Zeitrahmen M1 bis MN
- Eigener UDF-Datafeed auf die Broker-API: historische Kerzen (OHLCV) und
  laufende Quotes (Bid/Ask) per WebSocket
- Der Datenlieferant ist austauschbar; hinter dem Datafeed kennt die Anwendung
  nur noch Symbole, Zeitrahmen und Bars
- Chart-Zustand und Zeichnungen werden je Konto gespeichert

### Analysen werden gezeichnet, nicht nur aufgelistet

Ergebnisse landen als **Linien, Zonen und Trendlinien** im Kursbild — im
passenden Zeitrahmen und in Farben, die zum gewählten Thema passen. Es gibt
zwei Wege: eine **Schnell-Analyse**, die direkt zeichnet, und eine
**Tiefen-Analyse** über die vollständige Multi-Agent-Pipeline, die archiviert
und später bewertet wird.

Die Analyse-Maschine ist ein Fork von
[TauricResearch/TradingAgents](https://github.com/TauricResearch/TradingAgents)
(Apache-2.0), angesprochen über eine OpenAI-kompatible Schnittstelle.

### Module

Ein abschaltbares Dock in der rechten Spalte. Nach aussen heissen die Module
**Analyse**, **Alarme** und **Elliott Wave**; jedes lässt sich je Konto
einzeln freigeben.

| Modul | Was es tut |
|---|---|
| **Analyse** | Analysen starten und ansehen, Zeichnungen im Chart ein- und ausschalten; der Agent im Chart gehört dazu |
| **Alarme** | Kursmarken überwachen und melden, wenn der Kurs sie erreicht |
| **Elliott Wave** | Wellenzählung und die daraus abgeleiteten Marken im Chart |

### Agent im Chart

Ein Gesprächsfenster neben dem Kurs. Der Agent sieht denselben Stand wie der
Nutzer — Kurs, Plan, die letzten Analysen — und antwortet dreistufig: zuerst
ein kurzer Block in fünf Zeilen (Instrument, Richtung, Einstieg, Stop, Ziel,
CRV), darunter zwei bis drei Sätze zur Strategie, danach die Begründung.
Fehlt eine Zahl, steht dort ein Gedankenstrich — geschätzt wird nicht.

Dazu: ein Fragenmenü, das Analysen selbst auslösen kann, eine Ausbruchswache
und Vorlesen der Antwort — die Sprachausgabe läuft lokal auf dem Gerät, nicht
über einen Cloud-Dienst.

Jede Antwort endet mit dem Hinweis, dass sie keine Anlageberatung ist.

### Nachrichten und Wirtschaftskalender

- **Schlagzeilen** je Instrument, ins Deutsche übersetzt und zwischengespeichert
- **Wirtschaftskalender** mit Vor- und Nachlauffenstern je Wichtigkeit; die
  Termine erscheinen im Chart und im Panel
- Ein **Regelwerk entscheidet, ob** überhaupt analysiert wird — kurz vor und
  nach wichtigen Terminen bleibt es still, statt in die Nachricht hinein zu
  rechnen. Fällige Nachläufe werden nachgeholt.

### Indikatoren, Trades und Bewertung

- Verwaltung der **TradingView-Pine-Indikatoren** in zwei getrennten Schichten:
  die Fassung von TradingView und die eigene Erweiterung. Ein Update von
  TradingView überschreibt die eigene Schicht nicht.
- **Trade-Import** aus dem Transaktionsexport des Brokers; Teilausführungen
  werden nach FIFO zu Trades zusammengefasst
- **Trade-Bewertung**: alle Kennzahlen (CRV, R-Vielfache, Haltedauer, MFE/MAE)
  rechnet die Anwendung selbst; das Sprachmodell liefert nur die Erläuterung
- **Lernschleife**: alte Pläne werden Wochen später gegen die tatsächlichen
  Kurse bewertet

### Benachrichtigungen

- **Push** über [ntfy](https://ntfy.sh) — quelloffen, kein Konto nötig,
  innerhalb von Sekunden; enthält den Anlass
- **E-Mail** — der volle Text, zum Nachlesen und Ablegen

Ausgelöst wird, wenn eine Kursmarke erreicht wird, eine Analyse fertig ist
oder ein Stop in Reichweite kommt.

### Konto, Freischaltung und Abrechnung

- Anmeldung mit **Zwei-Faktor-Bestätigung (TOTP)** und Recovery-Codes
- **Freischaltprozess**: ein neues Konto bestätigt zuerst seine
  E-Mail-Adresse, erhält danach Leserechte auf das Modul Analyse und wird von
  Hand geprüft. Das Schreibrecht wird nicht automatisch erteilt.
- **Tarife und Abonnement** mit Zahlungssperre: bleibt die Zahlung aus, ruhen
  nur die rechenintensiven Funktionen; der lesende Zugang bleibt bestehen
- **Schweizer QR-Rechnung**
- **Verwaltungsbereich** für Konten, Freigaben je Modul, Tarifanfragen und
  Rechnungen
- **Handbuch** als eigene Dokumentation im Dienst

### Weiteres

Newsletter mit Bestätigungslink (Double Opt-in) und Ein-Klick-Abmeldung,
Rückruf- und Kontaktformular, eine Übersicht aller automatischen Vorgänge
(Zeitpläne für Kursaktualisierung und Analyse-Auslöser) sowie eine
Volltextsuche über das mitgelieferte Handbuch.

---

## Stufen — Entwurf, noch nicht festgelegt

> **Dies ist ein Vorschlag, keine Preis- oder Leistungszusage.** Die
> Staffelung ist noch nicht entschieden. Sie orientiert sich an dem, was
> heute technisch schon vorhanden ist: den drei Modulen mit den Stufen
> *kein Zugriff / ansehen / arbeiten*, den Fragen-Kontingenten und der Zahl
> der beobachteten Instrumente. **Preise stehen hier bewusst keine**, weil
> derzeit nicht abgerechnet wird.

Die Spalte **heute** sagt, ob es die Funktion in der Anwendung bereits gibt
oder ob sie für diese Stufe erst noch gebaut werden müsste.

| | Frei | Pro | Team | Enterprise | heute |
|---|---|---|---|---|---|
| Chart, Zeichnungen, gespeichertes Layout | ✓ | ✓ | ✓ | ✓ | vorhanden |
| Modul **Analyse** — gespeicherte Analysen ansehen | ✓ | ✓ | ✓ | ✓ | vorhanden |
| Modul **Analyse** — eigene Analysen starten | — | ✓ | ✓ | ✓ | vorhanden |
| **Agent im Chart** (gehört zur Analyse) | — | ✓ | ✓ | ✓ | vorhanden |
| Modul **Alarme** | — | ✓ | ✓ | ✓ | vorhanden |
| Modul **Elliott Wave** | — | ✓ | ✓ | ✓ | vorhanden |
| Benachrichtigung per **E-Mail** | — | ✓ | ✓ | ✓ | vorhanden |
| **Push** über ntfy | — | ✓ | ✓ | ✓ | vorhanden |
| Beobachtete **Instrumente** | eines | mehrere | mehrere | frei | Feld vorhanden, siehe Hinweis |
| **Fragen** an den Agenten | keine — das Modul ist nur zum Ansehen | Kontingent pro Stunde | Kontingent pro Stunde | nach Absprache | Feld vorhanden, siehe Hinweis |
| **Tiefen-Analyse** (volle Pipeline, archiviert) | — | ✓ | ✓ | ✓ | vorhanden |
| Mehrere Konten unter **einer Rechnung** | — | — | ✓ | ✓ | zu bauen |
| Freigaben je Konto durch eine **eigene Verwaltung** | — | — | ✓ | ✓ | Verwaltung vorhanden, Mandantentrennung zu bauen |
| Gemeinsame Instrumenten- und Alarmliste | — | — | ✓ | ✓ | zu bauen |
| **Eigene Instanz** auf eigener Infrastruktur | — | — | — | ✓ | Container vorhanden, Ausrollen zu klären |
| **Eigener Broker-Zugang** statt des gemeinsamen | — | — | — | ✓ | Datafeed ist austauschbar, zweiter Anschluss zu bauen |
| Abstimmung auf einzelne Instrumente und Abläufe | — | — | — | ✓ | nach Absprache |

**Hinweis zu den Kontingenten:** Die Spalten *Instrumente* und *Fragen* stehen
heute zwar in der Tariftabelle und auf der Kontoseite, werden aber **noch
nicht durchgesetzt**. Es gilt derzeit für alle Konten dieselbe Grenze.
Wirklich vom Tarif abhängig sind heute die Modulrechte und der Zugang bei
fehlender Zahlung.

**Zur Verfügbarkeit** wird an keiner Stelle etwas zugesagt — auch nicht in den
oberen Stufen. Es besteht kein Anspruch auf ununterbrochenen Betrieb; das ist
in den Nutzungsbedingungen so geregelt.

---

## Keine Anlageberatung

Der folgende Wortlaut stammt aus den
[Nutzungsbedingungen](rechtliches/bedingungen/index.html), Abschnitt
*Keine Anlageberatung*:

> Die in Kontor dargestellten Analysen, Signale, Kursmarken, Wellenzählungen
> und die Antworten des Agenten sind ALLGEMEINE INFORMATIONEN. Sie sind keine
> persönliche Empfehlung, kein Angebot und keine Aufforderung zum Kauf oder
> Verkauf eines Finanzinstruments.
>
> Es wird ausdrücklich weder Anlageberatung noch Vermögensverwaltung im Sinne
> des Bundesgesetzes über die Finanzdienstleistungen (FIDLEG) erbracht. Es
> findet keine Prüfung der Eignung oder Angemessenheit für die persönlichen
> Verhältnisse, Kenntnisse, Erfahrungen oder Anlageziele des Nutzers statt.
>
> Jede Handelsentscheidung trifft der Nutzer selbst, in eigener Verantwortung
> und auf eigenes Risiko.
>
> Der Handel mit gehebelten Produkten — insbesondere CFDs, Devisen und
> Derivaten — ist mit erheblichen Risiken verbunden. Er kann zum vollständigen
> Verlust des eingesetzten Kapitals und je nach Produkt und Broker zu
> Verlusten darüber hinaus (Nachschusspflicht) führen.
>
> Vergangene Ergebnisse, Rückrechnungen und Beispielanalysen sind kein Hinweis
> auf künftige Ergebnisse.

Die Antworten des Agenten werden maschinell erzeugt. Sie können unvollständig,
veraltet oder sachlich falsch sein und ersetzen keine eigene Prüfung.

Kurse, Instrumentendaten und Nachrichten stammen von Dritten. Angezeigte Kurse
sind nicht handelbar und keine Zusicherung eines ausführbaren Preises.

---

## Stand

Kontor befindet sich im **Beta-Betrieb** und wird derzeit als
**nicht-kommerzielles Startup-, Hobby- und Forschungsprojekt** geführt. **Es
findet keine Abrechnung statt**: bis heute wurde keine einzige Rechnung
ausgestellt. Die angelegten Tarife und Abonnements sind nicht in Gebrauch.

Sobald abgerechnet wird, ändert sich dieser Status, und die Rechtstexte werden
angepasst. Der Abschnitt *Betriebsstatus* in den Nutzungsbedingungen
beschreibt den jeweils aktuellen Stand.

---

## Rechtliches

Die drei Seiten liegen in diesem Repository unter `rechtliches/`:

- [Impressum](rechtliches/impressum/index.html)
- [Datenschutzerklärung](rechtliches/datenschutz/index.html)
- [Nutzungsbedingungen](rechtliches/bedingungen/index.html)

Massgebend ist jeweils die im Netz veröffentlichte Fassung. Die Kopien hier
geben den Stand vom 3. September 2026 wieder.

Es gilt schweizerisches Recht; Gerichtsstand ist Sursee, Kanton Luzern.
Zwingende Gerichtsstände, insbesondere für Konsumentinnen und Konsumenten,
bleiben vorbehalten.
