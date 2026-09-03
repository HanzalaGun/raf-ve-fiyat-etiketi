[Türkçe](README.md) · **English** · [Русский](README.ru.md) · [Deutsch](README.de.md) · [العربية](README.ar.md)

# Shelf and price label maker

Turns a product list into shelf price labels — it **works out the unit price
itself** and names any legally required field you left blank. A single
`index.html` file; no install, no build step, no server.

**Live version:** [hanzala.com.tr/araclar/raf-ve-fiyat-etiketi](https://hanzala.com.tr/araclar/raf-ve-fiyat-etiketi)

<br>

## Its real job isn't pretty labels

Turkey's price-label regulation (*Fiyat Etiketi Yönetmeliği*), **article 5/2**,
requires a label to carry:

- where the goods were produced
- the distinguishing feature of the goods (here, the product name)
- the sale price including all taxes
- the **unit price**
- the date the price came into effect

This tool computes the unit price for you and tells you, product by product,
which of the above is missing. It still prints the label — blocking a print
because a field is empty corners the business; warning and getting out of the
way is the right call.

For discounts, **article 11**: the pre-discount price must be shown *together
with* the discounted price and legibly; that price is the **lowest price
applied in the ten days before** the discount (for perishables, the previous
price). This tool cannot know which price was the ten-day low — you enter it.
The tool only prints both, legibly, side by side.

<br>

## Unit price

Grams and millilitres are converted to kilos and litres first:

```
500 g, 180 ₺  →  360.00 ₺/kg
1 L, 240 ₺    →  240.00 ₺/L
3 items, 12 ₺ →    4.00 ₺/item
```

`0.045 ₺/g` is technically correct and tells a shopper nothing.

<br>

## Label colour

White, **yellow** (sale), **red** (clearance), green, orange — or your own
colour. In retail a colour carries information; it is not decoration.

**The text colour is chosen by measurement, not by eye.** The WCAG contrast
ratio of both black and white against the background is computed and the higher
one wins — black on yellow, white on dark green. Deciding by a luminance
threshold picks the wrong side in the mid tones.

There is a measured guarantee behind it: **whatever colour you pick, the
text-to-background contrast stays at or above 4.5:1**. The worst case is 4.59,
on a `#008900` background — a full 256-step sweep runs in the self-check. If the
displayed ratio is below 7:1 the tool says so; that is the meaningful line for a
label read from a metre away under variable store lighting.

The discount badge adapts too: the retail red is kept where it can be, and
inverted on a red label where it would otherwise disappear.

Article 9/1 requires the label to be **legible** — so contrast is a compliance
matter, not decoration.

A coloured background **survives printing**: browsers drop background colours by
default, so `print-color-adjust: exact` asks for them explicitly. A yellow label
printing white would make the whole colour choice pointless.

<br>

## Input format

One product per line, columns separated by a **tab**, **semicolon** or **pipe**:

```
Olive oil 1 L	240	1	L	280	Ayvalık
White cheese	180	500	g		Trakya
Ground coffee 100 g	45	100	g		İzmir
```

`name · price · amount · unit · old price · place of production`
Units: `kg` `g` `L` `ml` `adet` (item) `m`.

If a line has none of those three separators, it falls back to the comma. The
comma is deliberately the last resort: Turkish prices are written `145,90`, and
treating the comma as a separator would make the price 145 and the amount 90.

**A blank price and a zero price are not the same thing.** `Number('')` returns
zero and `Number.isFinite(0)` is true — without that guard, a price left empty
printed a silent "0.00 ₺" and raised no missing-field warning.

<br>

## CSV

The list lives in your browser and is never sent to a server — close the tab and
it is gone. Export a long list to CSV, then load the same file to carry on. If
you have typed anything, the browser warns you before the tab closes.

The file starts with a **BOM** (`EF BB BF`); without it Excel does not recognise
UTF-8 and "Zeytinyağı" becomes "ZeytinyaÄŸÄ±".

**Protected against CSV injection.** Excel and LibreOffice treat a cell starting
with `=`, `+`, `-` or `@` as a formula. An exported file can be passed on to
someone else, and a product name like `=cmd|' /c calc'!A1` would run on their
machine. Dangerous cells are locked to text with a single-quote prefix, which is
stripped again on import.

CSV rather than `.xlsx` on purpose: Excel opens `.csv` on a double click, so
there is no difference to the user. `.xlsx` would mean a third-party library
that unzips archives and parses XML — and this tool is built on the promise that
the data never leaves the browser.

<br>

## Languages

Turkish, English, Russian, German and Arabic — the same set the website uses.
Arabic is laid out right to left; the product list box stays left to right even
there, because the list is pasted out of a spreadsheet and the columns have to
keep the order they were pasted in.

Each language has its own address: `index.html?dil=en`. With no language chosen
the browser's own language is used, and Turkish if that is not one of the five.

<br>

## Using it

Download `index.html` and double-click it. No dependencies, works offline.

<br>

## Self-check

Open `index.html?test=1`. It checks the blank-versus-zero price distinction, the
unit conversions, the discount percentage, the CSV injection guard, a CSV
round-trip, the BOM at byte level, the contrast maths, the guarantee that the ink keeps AA
on every possible background (a full 256-step sweep), and every language's
translation table. If
one fails, the tab title becomes `HATA:`.

<br>

## Licence

MIT — [LICENSE](LICENSE). Use it, change it, sell it.

This tool was written for [hanzala.com.tr](https://hanzala.com.tr), where there
are [more free tools](https://hanzala.com.tr/araclar) too.
