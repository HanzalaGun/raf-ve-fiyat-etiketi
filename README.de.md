[Türkçe](README.md) · [English](README.en.md) · [Русский](README.ru.md) · **Deutsch** · [العربية](README.ar.md)

# Regal- und Preisschild-Generator

Macht aus einer Produktliste Preisschilder fürs Regal — **berechnet den
Grundpreis selbst** und benennt jedes gesetzlich verlangte Feld, das Sie leer
gelassen haben. Eine einzige `index.html`; keine Installation, kein Build, kein
Server.

**Laufende Fassung:** [hanzala.com.tr/araclar/raf-ve-fiyat-etiketi](https://hanzala.com.tr/araclar/raf-ve-fiyat-etiketi)

<br>

## Seine eigentliche Aufgabe ist nicht das hübsche Schild

Die türkische Preisauszeichnungsverordnung (*Fiyat Etiketi Yönetmeliği*),
**Artikel 5/2**, verlangt auf dem Schild:

- den Herstellungsort der Ware
- das unterscheidende Merkmal der Ware (hier der Produktname)
- den Verkaufspreis inklusive aller Steuern
- den **Grundpreis**
- das Datum, ab dem der Preis gilt

Das Werkzeug rechnet den Grundpreis für Sie aus und sagt Ihnen Produkt für
Produkt, was davon fehlt. Gedruckt wird trotzdem: einen Druck wegen eines leeren
Feldes zu blockieren treibt den Betrieb in die Enge — warnen und aus dem Weg
gehen ist richtig.

Beim Rabatt **Artikel 11**: der Preis vor dem Rabatt wird *zusammen* mit dem
rabattierten Preis und lesbar gezeigt; das ist der **niedrigste Preis der zehn
Tage davor** (bei leicht verderblicher Ware der vorherige Preis). Welcher Preis
das Zehn-Tage-Minimum war, kann dieses Werkzeug nicht wissen — den geben Sie
ein. Es druckt nur beide nebeneinander und lesbar.

<br>

## Grundpreis

Gramm und Milliliter werden zuerst in Kilo und Liter umgerechnet:

```
500 g, 180 ₺   →  360,00 ₺/kg
1 L, 240 ₺     →  240,00 ₺/L
3 Stück, 12 ₺  →    4,00 ₺/Stück
```

`0,045 ₺/g` ist technisch richtig und sagt am Regal niemandem etwas.

<br>

## Eingabeformat

Ein Produkt je Zeile, Spalten getrennt durch **Tabulator**, **Semikolon** oder
**senkrechten Strich**:

```
Olivenöl 1 L	240	1	L	280	Ayvalık
Weißkäse	180	500	g		Trakya
Gemahlener Kaffee 100 g	45	100	g		İzmir
```

`Name · Preis · Menge · Einheit · alter Preis · Herstellungsort`
Einheiten: `kg` `g` `L` `ml` `adet` (Stück) `m`.

Fehlt in einer Zeile jedes dieser drei Trennzeichen, wird auf das Komma
zurückgegriffen. Das Komma ist bewusst die letzte Wahl: türkische Preise werden
`145,90` geschrieben, und als Trennzeichen gelesen wäre der Preis 145 und die
Menge 90.

**Ein leerer Preis und ein Preis von null sind nicht dasselbe.** `Number('')`
gibt null zurück und `Number.isFinite(0)` ist wahr — ohne diese Prüfung wurde
ein leer gelassener Preis stillschweigend als „0,00 ₺" gedruckt, ganz ohne
Hinweis auf das fehlende Feld.

<br>

## CSV

Die Liste bleibt im Browser und wird nicht gesendet — Tab zu, Liste weg. Lange
Listen als CSV herunterladen, dieselbe Datei später wieder laden und
weitermachen. Wenn Sie etwas eingegeben haben, warnt der Browser vor dem
Schließen.

Die Datei beginnt mit einer **BOM** (`EF BB BF`); ohne sie erkennt Excel UTF-8
nicht und aus „Zeytinyağı" wird „ZeytinyaÄŸÄ±".

**Gegen CSV-Injection geschützt.** Excel und LibreOffice halten eine Zelle, die
mit `=`, `+`, `-` oder `@` beginnt, für eine Formel. Eine exportierte Datei kann
weitergegeben werden, und ein Produktname wie `=cmd|' /c calc'!A1` liefe dort.
Gefährliche Zellen werden mit einem Apostroph als Text verriegelt, beim Import
wird er wieder entfernt.

CSV statt `.xlsx` mit Absicht: Excel öffnet `.csv` per Doppelklick, für die
Benutzerin macht es keinen Unterschied. `.xlsx` hieße eine fremde Bibliothek,
die ZIP-Archive auspackt und XML liest — und dieses Werkzeug steht auf dem
Versprechen, dass die Daten den Browser nicht verlassen.

<br>

## Sprachen

Türkisch, Englisch, Russisch, Deutsch und Arabisch — derselbe Satz wie auf der
Website. Arabisch läuft von rechts nach links; das Feld mit der Produktliste
bleibt auch dort von links nach rechts, denn die Liste wird aus einer Tabelle
eingefügt und die Spalten müssen ihre Reihenfolge behalten.

Jede Sprache hat ihre eigene Adresse: `index.html?dil=de`. Ohne Auswahl gilt die
Browsersprache, und Türkisch, falls sie nicht dabei ist.

<br>

## Benutzung

`index.html` herunterladen und doppelklicken. Keine Abhängigkeiten, läuft auch
offline.

<br>

## Selbstprüfung

`index.html?test=1` öffnen. Geprüft werden die Unterscheidung von leerem und
Null-Preis, die Einheitenumrechnung, der Rabattprozentsatz, der
CSV-Injection-Schutz, ein CSV-Hin-und-Rückweg, die BOM auf Byte-Ebene und die
Übersetzungstabellen aller fünf Sprachen. Fällt eine Prüfung durch, wird der
Tab-Titel zu `HATA:`.

<br>

## Lizenz

MIT — [LICENSE](LICENSE). Nutzen, ändern, verkaufen.

Dieses Werkzeug entstand für [hanzala.com.tr](https://hanzala.com.tr); dort gibt
es auch [weitere kostenlose Werkzeuge](https://hanzala.com.tr/araclar).
