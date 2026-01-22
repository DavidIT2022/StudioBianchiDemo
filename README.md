# Studio Bianchi - Dataset Didattico

Dataset didattico per corsi di Business Intelligence con modello **Star Schema Kimball**.

## 🌐 Demo Live

**[https://davidit2022.github.io/StudioBianchiDemo/](https://davidit2022.github.io/StudioBianchiDemo/)**

## 📊 Il Caso

Uno studio professionale che offre servizi di consulenza contabile, fiscale e revisione:

- **12 professionisti** organizzati in 3 team
- **250 incarichi** con 180 clienti diversi
- **16.248 ore** registrate (2024-2025)
- **Storicizzazione** completa di ruoli, costi e tariffe

## 🏗️ Architettura

### Star Schema (Kimball)

```
                    ┌─────────────────┐
                    │   Dim_Data      │
                    │   (DataKey)     │
                    └────────┬────────┘
                             │
    ┌────────────────────────┼────────────────────────┐
    │                        │                        │
    ▼                        ▼                        ▼
┌─────────┐          ┌─────────────────┐      ┌──────────────┐
│Fact_    │          │Fact_Redditivita │      │Fact_         │
│Timesheet│          │   _Incarico     │      │CostiDiretti  │
└─────────┘          └─────────────────┘      └──────────────┘
```

### Dimensioni (7)

| Vista | Righe | Descrizione |
|-------|-------|-------------|
| `Dim_Data` | 731 | Calendario con DataKey YYYYMMDD |
| `Dim_Persona` | 32 | **SCD Type 2** - storico ruolo/costo/team |
| `Dim_Team` | 3 | Team dello studio |
| `Dim_Cliente` | 180 | Anagrafica clienti |
| `Dim_Incarico` | 250 | Denormalizzata con Cliente e TipoIncarico |
| `Dim_TipoCosto` | 10 | Tipologie costo diretto |
| `Dim_VoceCapitolato` | 64 | Voci capitolato |

### Fact Tables (4)

| Vista | Righe | Misure |
|-------|-------|--------|
| `Fact_Timesheet` | 16.248 | OreLavorate, CostoEffettivo, RicavoTeorico |
| `Fact_Redditivita_Incarico` | 250 | RicavoReale, RicavoTeorico, MargineReale, MargineDesiderato |
| `Fact_CostiDiretti` | 244 | Importo |
| `Fact_AvanzamentoCapitolato` | 1.364 | Peso, PesoCompletato |

## 🔗 API JSON

Tutti i dati sono disponibili come file JSON statici:

```
https://davidit2022.github.io/StudioBianchiDemo/data/starschema/Fact_Timesheet.json
https://davidit2022.github.io/StudioBianchiDemo/data/starschema/Dim_Persona.json
...
```

### Connessione da Power BI

```m
let
    Source = Json.Document(Web.Contents(
        "https://davidit2022.github.io/StudioBianchiDemo/data/starschema/Fact_Timesheet.json"
    )),
    Table = Table.FromList(Source, Splitter.SplitByNothing()),
    Expanded = Table.ExpandRecordColumn(Table, "Column1", Record.FieldNames(Table{0}[Column1]))
in
    Expanded
```

## 📁 Struttura Repository

```
StudioBianchiDemo/
├── index.html              # Documentazione interattiva
├── README.md
├── data/
│   ├── starschema/         # Modello Kimball
│   │   ├── Dim_Data.json
│   │   ├── Dim_Persona.json
│   │   ├── Fact_Timesheet.json
│   │   └── ...
│   └── didattica/          # Viste didattiche (replica M)
│       ├── Timesheet.json
│       ├── Calendario.json
│       └── ...
```

## 📝 Licenza

Materiale didattico - libero utilizzo per scopi formativi.
