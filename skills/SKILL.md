---
name: cost-transparency
description: Kostentransparenz-Analysator fuer Anlageprodukte. Zerlegt die Gesamtkosten von ETFs, Fonds, Versicherungsprodukten, 3a-Loesungen, Strukturierten Produkten und Robo-Advisors in ihre Einzelbestandteile (TER-Komponenten, Transaktionskosten, Spread, Performance Fee, Stempelsteuer, Quellensteuer-Drag). Berechnet die Kostenwirkung ueber Zeit (10/20/30 Jahre) und zeigt, wie viel Rendite durch Kosten verloren geht. Benchmarkt jedes Produkt gegen guenstigere Alternativen. Schweizer Kontext als Standard. Triggert bei Fragen zu Kosten, TER, Gebuehren, Kostentransparenz, Produktkosten, Spread, Kostenvergleich, was kostet mein Portfolio, versteckte Kosten, Tracking Difference, Performance Fee, Gebuehrenvergleich, Anlagekosten, Total Cost of Ownership, Kostenwirkung.
---

# Kostentransparenz — Anlagekosten-Analysator

## Overview

Dieser Skill macht die Gesamtkosten von Anlageprodukten vollstaendig transparent. Er geht weit ueber die TER hinaus: Versteckte Kosten (Spread, Tracking Difference, Performance Fee, Swing Pricing), Transaktionskosten, Stempel- und Quellensteuer-Drag werden systematisch erfasst. Der Kern ist die **Kostenwirkung ueber Zeit** — wie viel CHF Rendite gehen durch Kosten ueber 10, 20, 30 Jahre verloren?

**Standardkontext:** Schweiz (CHF, Stempelsteuer, Verrechnungssteuer, Saeule 3a). Bei anderen Laendern werden die Referenzdaten entsprechend angepasst.

**Abgrenzung zu asset-allocation:** Der asset-allocation Skill enthaelt eine Kostenanalyse auf Portfolioebene (gewichtete TER, Gesamtkosten). Dieser Skill geht tiefer: Einzelprodukt-Zerlegung, versteckte Kostenschichten, Produkt-Vergleiche, und vor allem die **Zeitwirkung** (CHF-Betraege, die ueber Jahre verloren gehen).

**Companion Skills:**
- **asset-allocation:** Liefert Produktliste und Allokation als Input (JSON-Import moeglich).
- **risk-profile:** Liefert Risikoprofil fuer Kontext-Einordnung.

**Wichtig:** Weise den Nutzer zu Beginn darauf hin, dass diese Kostenanalyse keine professionelle Finanz- oder Anlageberatung ersetzt. Kostenvergleiche und Optimierungsvorschlaege sind Orientierungshilfen — vor einer Umsetzung sollte eine individuelle Pruefung erfolgen.

## Workflow

Fuehre den Nutzer durch die folgenden 5 Phasen. Stelle in jeder Phase gezielte Fragen conversational (nicht als starres Formular) und fasse die Antworten zusammen, bevor du zur naechsten Phase uebergehst. Passe die Sprache an den Nutzer an (Deutsch/Englisch).

---

### Phase 1 — Produkt-Erfassung

Ziel: Anlageprodukte mit Basisdaten erfassen.

**1.1 Import oder manuelle Erfassung:**

Frage den Nutzer, ob ein Portfolio-Blueprint aus dem asset-allocation Skill vorliegt:
- **Ja, JSON vorhanden:** Nutzer stellt `portfolio-blueprint-*.json` bereit → importiere `instrumente.core[]`, `instrumente.satellite[]`, `saule_3a`, `kosten.*`. Bestaetige die importierten Produkte.
- **Nein, manuelle Erfassung:** Erfasse die Produkte einzeln.

**1.2 Produkte erfassen:**

Erfasse fuer jedes Anlageprodukt:

| Feld | Details |
|------|---------|
| Produkttyp | ETF / Aktiver Fonds / Versicherungs-Mantel (3b-Police) / Strukturiertes Produkt / Robo-Advisor / Bank-Vermoegensverwaltung / Saeule-3a-Loesung / Einzeltitel |
| Name / ISIN | Produktname und/oder ISIN-Nummer |
| Anbieter / Emittent | Fondsgesellschaft, Versicherung, Bank |
| Anlagebetrag (CHF) | Aktueller Wert oder geplante Investition |
| Bekannte Kosten | TER, Verwaltungsgebuehr, Ausgabekommission — was der Nutzer bereits kennt |
| Broker / Depotbank | Wo das Produkt gehalten wird (fuer Depot- und Transaktionskosten) |

**Unterstuetzte Produkttypen:**

| Produkttyp | Typische Kostenschichten |
|-----------|------------------------|
| ETF (Index) | TER, Spread, Tracking Difference, Stempelsteuer, Depotgebuehr |
| Aktiver Fonds | TER (hoeher), Performance Fee, Ausgabekommission, Spread, Stempelsteuer |
| Versicherungs-Mantel (3b) | Versicherungskosten, Verwaltung, eingebettete Fondskosten, Rueckkaufswert-Abzug |
| Strukturiertes Produkt | Emittenten-Marge, Hedging-Kosten, Spread, ggf. Coupon-Reduktion |
| Robo-Advisor | All-in-Fee, eingebettete ETF-Kosten, Rebalancing-Kosten |
| Bank-Vermoegensverwaltung | Verwaltungsmandat-Gebuehr, eingebettete Fondskosten, Courtagen, Retrozessionen |
| Saeule 3a (Wertschriften) | Verwaltungsgebuehr, eingebettete Fondskosten, ggf. Depot- und Wechselgebuehren |
| Einzeltitel (Aktien) | Courtage, Spread, Stempelsteuer, Depotgebuehr (keine laufende TER) |

Nach Erfassung: Produktliste mit Basisdaten zur Bestaetigung.

---

### Phase 2 — Kosten-Zerlegung

Ziel: Jedes Produkt in seine Kostenbestandteile zerlegen und einen Kosten-Wasserfall erstellen.

Lade `references/swiss-cost-reference.md` als Referenz.

**2.1 TER-Zerlegung:**

Fuer jeden Fonds/ETF: Zerlege die TER in ihre Komponenten (soweit bekannt/schaetzbar):

| TER-Komponente | Typischer Anteil | Beschreibung |
|---------------|-----------------|-------------|
| Verwaltungsgebuehr | 50-70% der TER | Fondsmanagement, Research, Admin |
| Depotbankgebuehr | 5-15% der TER | Verwahrung der Fondsvermoegen |
| Index-Lizenzgebuehr | 3-10% der TER (nur Index-ETFs) | Lizenz fuer Index-Nutzung (MSCI, FTSE etc.) |
| Vertriebsgebuehr / Retrozession | 0-30% der TER (aktive Fonds) | Vergütung an Vertriebspartner |
| Sonstige (Revision, Regulierung) | 5-10% der TER | Pruefungskosten, regulatorische Kosten |

**2.2 Kosten AUSSERHALB der TER:**

| Kostenschicht | Beschreibung | Typische Hoehe |
|--------------|-------------|---------------|
| **Transaktionskosten (fondsinttern)** | Kauf-/Verkaufskosten innerhalb des Fonds bei Rebalancing/Indexanpassung | 0.01-0.15% p.a. |
| **Bid-Ask-Spread** | Differenz zwischen Kauf- und Verkaufskurs beim Handel | 0.02-0.50% (einmalig pro Trade) |
| **Tracking Difference** | Tatsaechliche Abweichung vom Index (kann besser oder schlechter als TER sein) | -0.10% bis +0.50% p.a. |
| **Performance Fee** | Erfolgsabhaengige Gebuehr (nur aktive Fonds/Hedge Funds) | 10-20% der Outperformance |
| **Ausgabekommission (Agio)** | Einmalige Gebuehr beim Kauf (aktive Fonds, Bankvertrieb) | 0-5% einmalig |
| **Ruecknahmekommission** | Einmalige Gebuehr beim Verkauf | 0-1% einmalig |
| **Swing Pricing** | Verwässerungsschutz bei grossen Mittelzu-/-abfluessen | 0-0.50% (situativ) |
| **Depotgebuehr (Broker)** | Jaehrliche Verwahrungsgebuehr beim Broker | CHF 0-200/Jahr |
| **Courtage (Broker)** | Transaktionsgebuehr beim Kauf/Verkauf | CHF 1-50 pro Trade |
| **Stempelsteuer** | Eidg. Umsatzabgabe | 0.075% CH / 0.150% Ausland pro Trade |
| **Quellensteuer-Drag** | Nicht rueckforderbare Quellensteuer je nach Fondsdomizil und DBA | 0-0.30% p.a. |
| **Waehrungsumrechnungs-Kosten** | Spread bei Waehrungskonversion (Broker oder Fonds) | 0.10-0.50% pro Konversion |

**2.3 Kosten-Wasserfall erstellen:**

Erstelle pro Produkt einen Kosten-Wasserfall:

```
Produkt: {Name} ({ISIN})
Anlagebetrag: CHF {Betrag}
─────────────────────────────────────────
Laufende Kosten (p.a.):
  TER (ausgewiesen)              {x.xx}%
  + Transaktionskosten intern    {x.xx}%
  + Tracking-Difference-Aufschlag{x.xx}%
  + Performance Fee (geschaetzt) {x.xx}%
  + Quellensteuer-Drag           {x.xx}%
  ─────────────────────────────────
  = Effektive laufende Kosten    {x.xx}% = CHF {Betrag}/Jahr

Einmalige Kosten (pro Kauf):
  Ausgabekommission              {x.xx}%
  + Spread (geschaetzt)          {x.xx}%
  + Courtage (Broker)            CHF {x}
  + Stempelsteuer                {x.xx}%
  ─────────────────────────────────
  = Einmalige Kosten             {x.xx}% = CHF {Betrag}

Jaehrliche Depot-/Plattformkosten:
  Depotgebuehr                   CHF {x}/Jahr
  ─────────────────────────────────
  = Total jaehrliche Kosten      {x.xx}% = CHF {Betrag}/Jahr
```

**2.4 Spezial-Zerlegung nach Produkttyp:**

Fuer Versicherungs-Mantel (3b-Policen):
- Versicherungskosten (Risikopraemie, Admin) vs. Sparanteil
- Eingebettete Fondskosten auf dem Sparanteil
- Rueckkaufswert-Verlust bei vorzeitiger Kuendigung

Fuer Strukturierte Produkte:
- Emittenten-Marge (oft 1-3% implizit eingebettet)
- Hedging-Kosten (Optionspraemien, Dividendenverzicht)
- Coupon-Reduktion durch Kosten

Fuer Robo-Advisors:
- All-in-Fee (Management-Gebuehr)
- Plus eingebettete ETF-TERs (Doppelschicht!)
- Rebalancing-Transaktionskosten

Nach Phase 2: Kosten-Wasserfall pro Produkt zur Bestaetigung.

---

### Phase 3 — Vergleich & Benchmark

Ziel: Jedes Produkt gegen die guenstigste Alternative benchmarken und das Einsparpotenzial berechnen.

**3.1 Einzelprodukt-Benchmark:**

Vergleiche jedes Produkt mit der guenstigsten vergleichbaren Alternative:

| Aktuelles Produkt | Benchmark-Alternative | Typischer Vergleich |
|-------------------|----------------------|---------------------|
| Aktiver Fonds (Aktien CH) | ETF auf gleichen Markt (z.B. iShares SPI, TER 0.10%) | TER-Differenz + Performance-Fee + Ausgabekommission |
| Aktiver Fonds (Aktien Global) | ETF MSCI World (z.B. SPDR, TER 0.12%) | TER-Differenz + Tracking-Bilanz |
| Bank-3a (Zinskonto) | VIAC/finpension (Wertschriften-3a) | Rendite-Differenz ueber Zeit |
| Bank-3a (Wertschriften, ~1.0%) | finpension (All-in 0.39%) | Kosten-Differenz × Betrag |
| Versicherungs-3b-Police | Kauf Risiko-Police separat + ETF-Sparplan | Gesamtkosten-Vergleich |
| Bank-Vermoegensverwaltung (1.5%) | DIY-ETF-Portfolio (0.20%) | Kosten-Differenz × Betrag |
| Robo-Advisor (0.60-0.80%) | DIY-ETF-Portfolio (0.20%) | Bequemlichkeitspraemie berechnen |
| Strukturiertes Produkt | Direktanlage + Option (falls nachbildbar) | Implizite Marge aufzeigen |

**3.2 Vergleichstabelle erstellen:**

| Produkt | Aktuelle Kosten (p.a.) | Guenstigste Alternative | Kosten Alternative (p.a.) | Differenz (p.a.) | Differenz (CHF/Jahr) |
|---------|----------------------|------------------------|--------------------------|-----------------|---------------------|
| {Name} | {x.xx}% | {Alternative} | {x.xx}% | {x.xx}% | CHF {Betrag} |
| ... | ... | ... | ... | ... | ... |
| **Total Portfolio** | **{x.xx}%** | **Optimiertes Portfolio** | **{x.xx}%** | **{x.xx}%** | **CHF {Betrag}** |

**3.3 Gesamtportfolio-Kosten vs. Benchmark:**

Vergleiche die Gesamtkosten mit drei Referenz-Modellen:

| Modell | Typische Gesamtkosten p.a. | Beschreibung |
|--------|---------------------------|-------------|
| **DIY-ETF-Portfolio (Discount-Broker)** | 0.15-0.30% | Eigenstaendige Verwaltung, 5-8 Index-ETFs, Rebalancing selbst |
| **Robo-Advisor (CH)** | 0.50-0.80% | Automatisierte Verwaltung, ETF-basiert, Rebalancing inklusive |
| **Klassische Bankberatung (CH)** | 1.00-1.80% | Persoenliche Beratung, oft aktive Fonds, Retrozessionen |

**3.4 Einsparpotenzial berechnen:**

```
Aktuelles Portfolio:     CHF {Total} × {x.xx}% = CHF {Kosten}/Jahr
Optimiertes Portfolio:   CHF {Total} × {x.xx}% = CHF {Kosten}/Jahr
────────────────────────────────────────────────
Jaehrliches Einsparpotenzial:  CHF {Differenz}/Jahr
```

Nach Phase 3: Vergleichstabellen und Einsparpotenzial zur Bestaetigung.

---

### Phase 4 — Kostenwirkung ueber Zeit

Ziel: Simulieren, wie viel CHF durch Kosten ueber 10/20/30 Jahre verloren gehen. Der Zinseszins-Effekt der Kosten ist das Kernargument dieses Skills.

Verwende `references/swiss-cost-reference.md` Abschnitt Zeitwirkungs-Formeln.

**4.1 Rendite-Szenarien definieren:**

Definiere mit dem Nutzer eine erwartete Brutto-Rendite (vor Kosten):

| Szenario | Brutto-Rendite | Typisch fuer |
|----------|---------------|-------------|
| Konservativ | 3.0% p.a. | Mischportfolio, hoher Obligationen-Anteil |
| Moderat | 5.0% p.a. | Ausgewogenes Aktien/Obligationen-Portfolio |
| Optimistisch | 7.0% p.a. | Aktien-lastiges Portfolio |

**4.2 Compound-Cost-Simulation:**

Berechne fuer jedes Kostenszenario (aktuell vs. optimiert) den Endwert:

```
Formel: Endwert = Startbetrag × (1 + Rendite_nach_Kosten)^Jahre

Rendite_nach_Kosten = Brutto_Rendite - Kosten_p.a.
```

**4.3 Zeitwirkungs-Tabelle (Hauptdarstellung):**

Erstelle die zentrale Zeitwirkungs-Tabelle:

| Zeitraum | Aktuell ({x.xx}% Kosten) | Optimiert ({x.xx}% Kosten) | Differenz (CHF) | Differenz (%) |
|----------|--------------------------|---------------------------|----------------|--------------|
| Startbetrag | CHF {Betrag} | CHF {Betrag} | CHF 0 | 0% |
| Nach 10 Jahren | CHF {Wert} | CHF {Wert} | CHF {Diff} | {x.x}% |
| Nach 20 Jahren | CHF {Wert} | CHF {Wert} | CHF {Diff} | {x.x}% |
| Nach 30 Jahren | CHF {Wert} | CHF {Wert} | CHF {Diff} | {x.x}% |

**4.4 Rendite-nach-Kosten-Vergleich:**

| Szenario | Brutto-Rendite | Kosten aktuell | Netto aktuell | Kosten optimiert | Netto optimiert | Rendite-Gewinn |
|----------|---------------|---------------|--------------|-----------------|----------------|---------------|
| Konservativ (3%) | 3.0% | {x.xx}% | {x.xx}% | {x.xx}% | {x.xx}% | +{x.xx}% |
| Moderat (5%) | 5.0% | {x.xx}% | {x.xx}% | {x.xx}% | {x.xx}% | +{x.xx}% |
| Optimistisch (7%) | 7.0% | {x.xx}% | {x.xx}% | {x.xx}% | {x.xx}% | +{x.xx}% |

**4.5 Sparbeitrag-Simulation (optional):**

Falls der Nutzer regelmaessig investiert, berechne auch den Effekt bei laufenden Sparbetraegen:

```
Endwert_Sparplan = Sparrate × ((1 + r_netto)^n - 1) / r_netto

Mit: r_netto = (Brutto_Rendite - Kosten) / 12 (monatlich)
     n = Jahre × 12
```

| Sparrate CHF/Mt. | Zeitraum | Aktuell (Kosten {x.xx}%) | Optimiert (Kosten {x.xx}%) | Differenz |
|-----------------|----------|--------------------------|---------------------------|-----------|
| CHF {Betrag} | 10 Jahre | CHF {Wert} | CHF {Wert} | CHF {Diff} |
| CHF {Betrag} | 20 Jahre | CHF {Wert} | CHF {Wert} | CHF {Diff} |
| CHF {Betrag} | 30 Jahre | CHF {Wert} | CHF {Wert} | CHF {Diff} |

**4.6 Kosten-Rendite-Verhältnis:**

Berechne, welchen Anteil der Brutto-Rendite die Kosten auffressen:

```
Kosten-Rendite-Verhaeltnis = Kosten_p.a. / Brutto_Rendite × 100%

Beispiel: 1.5% Kosten bei 5% Brutto-Rendite → 30% der Rendite gehen an Kosten!
```

Nach Phase 4: Zeitwirkungs-Tabellen und Simulationsergebnisse zur Bestaetigung.

---

### Phase 5 — Transparenz-Bericht & Optimierung

Erstelle zwei Ausgabedokumente:

#### 5.1 Markdown-Bericht

Speichere als `kostentransparenz-{name}-{datum}.md`:

```markdown
# Kostentransparenz-Bericht — {Name}
*Erstellt am {Datum}*

## Disclaimer
Dieser Kostentransparenz-Bericht ersetzt keine professionelle Finanz- oder Anlageberatung.
Kostenvergleiche und Optimierungsvorschlaege sind Orientierungshilfen — vor einer Umsetzung
sollte eine individuelle Pruefung erfolgen. Fuer eine verbindliche Beratung wende dich an
einen zugelassenen Finanzberater.

## 1. Analysierte Produkte

| # | Produkt | Typ | ISIN | Betrag (CHF) | Broker |
|---|---------|-----|------|-------------|--------|
| 1 | ... | ETF | ... | ... | ... |
| 2 | ... | Aktiver Fonds | ... | ... | ... |
| ... | ... | ... | ... | ... | ... |
| | **Total** | | | **CHF ...** | |

## 2. Kosten-Zerlegung

### Produkt 1: {Name}
{Kosten-Wasserfall aus Phase 2}

### Produkt 2: {Name}
{Kosten-Wasserfall aus Phase 2}

### Portfolio-Gesamtkosten
| Kostenart | Betrag / Rate |
|-----------|-------------|
| Gewichtete laufende Kosten (TER + versteckte Kosten) | {x.xx}% p.a. = CHF .../Jahr |
| Depot-/Plattformkosten | CHF .../Jahr |
| Geschaetzte Transaktionskosten (Rebalancing) | CHF .../Jahr |
| **Jaehrliche Gesamtkosten** | **{x.xx}% p.a. = CHF .../Jahr** |

## 3. Vergleich & Benchmark

### Einzelprodukt-Vergleich
| Produkt | Aktuelle Kosten | Guenstigste Alternative | Kosten Alternative | Differenz |
|---------|---------------|------------------------|--------------------|-----------|
| ... | ...% | ... | ...% | ...% |

### Gesamtportfolio vs. Referenzmodelle
| Modell | Kosten p.a. | vs. aktuelles Portfolio |
|--------|------------|----------------------|
| DIY-ETF (Discount-Broker) | 0.15-0.30% | ... |
| Robo-Advisor (CH) | 0.50-0.80% | ... |
| Klassische Bankberatung | 1.00-1.80% | ... |

### Einsparpotenzial
Jaehrliches Einsparpotenzial: **CHF .../Jahr** ({x.xx}% → {x.xx}%)

## 4. Kostenwirkung ueber Zeit

### Szenario: Moderates Wachstum ({x}% Brutto-Rendite)
| Zeitraum | Aktuell ({x.xx}% Kosten) | Optimiert ({x.xx}% Kosten) | Mehr Vermoegen |
|----------|--------------------------|---------------------------|---------------|
| Heute | CHF ... | CHF ... | CHF 0 |
| 10 Jahre | CHF ... | CHF ... | **CHF ...** |
| 20 Jahre | CHF ... | CHF ... | **CHF ...** |
| 30 Jahre | CHF ... | CHF ... | **CHF ...** |

### Rendite-nach-Kosten
| Szenario | Brutto | Netto aktuell | Netto optimiert | Gewinn |
|----------|--------|--------------|----------------|--------|
| Konservativ (3%) | 3.0% | ...% | ...% | +...% |
| Moderat (5%) | 5.0% | ...% | ...% | +...% |
| Optimistisch (7%) | 7.0% | ...% | ...% | +...% |

### Kosten-Rendite-Verhaeltnis
Aktuell fressen die Kosten **{x}%** der erwarteten Brutto-Rendite auf.
Nach Optimierung waeren es nur noch **{x}%**.

## 5. Optimierungsvorschlaege

### Sofortmassnahmen (hoechstes Einsparpotenzial)
1. {Massnahme 1} → Einsparung: CHF .../Jahr
2. {Massnahme 2} → Einsparung: CHF .../Jahr

### Mittelfristige Massnahmen
1. {Massnahme}
2. {Massnahme}

### Nicht empfohlen / abwaegen
- {Massnahme, die wenig bringt oder Risiken hat}

## 6. Annahmen & Methodik
- Brutto-Rendite-Annahme: {x}% p.a. (vor Kosten, nominal)
- Inflation: nicht beruecksichtigt (alle Werte nominal)
- Kosten-Schaetzungen basieren auf oeffentlich verfuegbaren Daten (KIID/KID, Factsheets)
- Tracking Difference und fondsintterne Transaktionskosten sind Schaetzwerte
- Quellensteuer-Drag basiert auf Fondsdomizil und DBA-Situation
- Stempelsteuer: CH-Saetze (0.075% CH / 0.150% Ausland)
- Waehrungskosten: Geschaetzt basierend auf typischen Broker-Spreads
```

#### 5.2 JSON-Export

Speichere als `kostentransparenz-{name}-{datum}.json`:

```json
{
  "meta": {
    "erstellt": "YYYY-MM-DD",
    "version": "1.0",
    "waehrung": "CHF",
    "land": "CH",
    "typ": "kostentransparenz"
  },
  "produkte": [
    {
      "name": "...",
      "typ": "etf",
      "isin": "...",
      "anbieter": "...",
      "betrag": 50000,
      "broker": "...",
      "kosten": {
        "ter_pct": 0.20,
        "ter_zerlegung": {
          "verwaltung_pct": 0.12,
          "depotbank_pct": 0.03,
          "index_lizenz_pct": 0.02,
          "vertrieb_pct": 0.00,
          "sonstige_pct": 0.03
        },
        "transaktionskosten_intern_pct": 0.03,
        "tracking_difference_pct": 0.05,
        "performance_fee_pct": 0.00,
        "quellensteuer_drag_pct": 0.00,
        "spread_pct": 0.05,
        "ausgabekommission_pct": 0.00,
        "effektive_laufende_kosten_pct": 0.28,
        "effektive_laufende_kosten_chf": 140,
        "einmalige_kosten_pct": 0.20,
        "einmalige_kosten_chf": 100,
        "depotgebuehr_chf": 0,
        "total_jaehrliche_kosten_pct": 0.28,
        "total_jaehrliche_kosten_chf": 140
      },
      "benchmark": {
        "alternative": "...",
        "alternative_kosten_pct": 0.15,
        "differenz_pct": 0.13,
        "differenz_chf": 65
      }
    }
  ],
  "portfolio_gesamt": {
    "total_betrag": 200000,
    "gewichtete_laufende_kosten_pct": 0.35,
    "depotkosten_chf": 0,
    "rebalancing_kosten_chf": 50,
    "total_jaehrliche_kosten_pct": 0.38,
    "total_jaehrliche_kosten_chf": 760
  },
  "optimiert": {
    "total_jaehrliche_kosten_pct": 0.18,
    "total_jaehrliche_kosten_chf": 360,
    "einsparpotenzial_pct": 0.20,
    "einsparpotenzial_chf": 400
  },
  "zeitwirkung": {
    "brutto_rendite_pct": 5.0,
    "szenarien": [
      {
        "jahre": 10,
        "endwert_aktuell": 0,
        "endwert_optimiert": 0,
        "differenz_chf": 0,
        "differenz_pct": 0
      },
      {
        "jahre": 20,
        "endwert_aktuell": 0,
        "endwert_optimiert": 0,
        "differenz_chf": 0,
        "differenz_pct": 0
      },
      {
        "jahre": 30,
        "endwert_aktuell": 0,
        "endwert_optimiert": 0,
        "differenz_chf": 0,
        "differenz_pct": 0
      }
    ],
    "kosten_rendite_verhaeltnis_aktuell_pct": 0,
    "kosten_rendite_verhaeltnis_optimiert_pct": 0
  },
  "optimierungsvorschlaege": [
    {
      "massnahme": "...",
      "kategorie": "sofort",
      "einsparung_chf_jahr": 0,
      "aufwand": "gering"
    }
  ]
}
```

Speichere beide Dateien im aktuellen Workspace-Verzeichnis.

---

## Wichtige Hinweise

- **Keine Anlageberatung:** Weise den Nutzer zu Beginn und im Bericht darauf hin, dass dieser Kostentransparenz-Bericht keine professionelle Finanz- oder Anlageberatung ersetzt. Kostenvergleiche und Optimierungsvorschlaege sind Orientierungshilfen.
- **Companion zu asset-allocation:** Wenn ein Portfolio-Blueprint vorliegt, importiere die Produktdaten. Empfehle bei Bedarf, zuerst den asset-allocation Skill zu durchlaufen.
- **Konservative Kosten-Schaetzungen:** Verwende im Zweifel hoehere Kostenannahmen (besser konservativ als zu optimistisch). Mache Schaetzungen transparent.
- **Tracking Difference statt TER:** Weise darauf hin, dass die Tracking Difference aussagekraeftiger ist als die TER. Verwende TD wo verfuegbar.
- **Iterativ:** Der Nutzer kann jederzeit zurueckspringen und Produkte hinzufuegen/aendern.
- **Laenderanpassung:** Wenn der Nutzer nicht in der Schweiz lebt, passe Stempelsteuer, Quellensteuer-Situation und Broker-Kosten an (z.B. Deutschland: kein Stempelsteuer, aber Vorabpauschale; Oesterreich: KESt).
- **Datenschutz:** Alle Daten bleiben lokal. Es werden keine Daten an externe Dienste gesendet.
- **Produktneutralitaet:** Die genannten Produkte und Alternativen sind Beispiele. Es besteht keine Verbindung zu Anbietern.
- **Grenzen der Analyse:** Manche Kosten (fondsintterne Transaktionskosten, exakter Quellensteuer-Drag) koennen nur geschaetzt werden. Mache dies im Bericht transparent.
