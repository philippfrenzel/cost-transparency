# cost-transparency

Kostentransparenz-Analysator fuer Anlageprodukte — ein [OpenClaw](https://openclaw.com) Skill.

## Was macht dieser Skill?

Zerlegt die Gesamtkosten von Anlageprodukten (ETFs, aktive Fonds, Versicherungsprodukte, 3a-Loesungen, Strukturierte Produkte, Robo-Advisors) in ihre Einzelbestandteile und berechnet die **Kostenwirkung ueber Zeit**.

### 5-Phasen-Dialog

1. **Produkt-Erfassung** — Anlageprodukte mit Basisdaten erfassen (Import aus asset-allocation oder manuell)
2. **Kosten-Zerlegung** — TER-Komponenten, versteckte Kosten (Spread, Tracking Difference, Performance Fee, Quellensteuer-Drag)
3. **Vergleich & Benchmark** — Jedes Produkt gegen guenstigste Alternative benchmarken
4. **Kostenwirkung ueber Zeit** — Simulation ueber 10/20/30 Jahre: Wie viel CHF gehen verloren?
5. **Transparenz-Bericht** — Markdown-Bericht + JSON-Export mit Optimierungsvorschlaegen

### Highlights

- TER-Zerlegung in Verwaltung, Depotbank, Index-Lizenz, Vertrieb
- Kosten AUSSERHALB der TER: Transaktionskosten, Spread, Stempelsteuer, Quellensteuer-Drag
- Compound-Cost-Simulation: Zinseszins-Effekt der Kosten ueber Jahrzehnte
- Schweizer Referenzdaten: Broker-Courtagen, Stempelsteuer, 3a-Vergleich, Robo-Advisor-Kosten

## Installation

```bash
# Via ayaiay CLI
ayaiay install philippfrenzel/cost-transparency
```

Oder manuell als OpenClaw Workspace Skill.

## Companion Skills

- [risk-profile](https://github.com/philippfrenzel/risk-profile) — Risikoprofil-Ersteller
- [asset-allocation](https://github.com/philippfrenzel/asset-allocation) — Portfolio-Blueprint-Planer
- [life-planner](https://github.com/philippfrenzel/life-planner) — Zielbild- & Lebensphasen-Planer

## Lizenz

MIT
