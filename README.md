# 🌱 Plant Company - Performance Analysis Dashboard

Dashboard interattiva Power BI per l'analisi delle performance di vendita di una plant company, con confronto temporale Year-To-Date (YTD) vs Previous Year-To-Date (PYTD) dal 2022 al 2024.

![Dashboard Preview](Dashboard_Screenshot.png)

## 📊 Overview del Progetto

Questo progetto dimostra competenze avanzate in:
- **Data Modeling** con schema a stella ottimizzato
- **Power Query** per la trasformazione e pulizia dei dati
- **DAX** per misure dinamiche e calcoli temporali

### KPI Principali
- **Vendite**: Analisi del fatturato con confronto YoY
- **Quantità**: Monitoraggio volumi di vendita
- **Profitto Lordo**: Marginalità e redditività

### Periodo di Analisi
📅 **2022 - 2024** (navigabile tramite slicer temporale)

---

## 🎯 Caratteristiche Principali

### Visualizzazioni Dinamiche
- **Treemap Geografica**: Ultimi 10 paesi per performance con codifica a colori per variazione YTD vs PYTD
- **Waterfall Chart**: Analisi mensile dell'andamento con incrementi/decrementi per mese, paese e prodotto
- **Stacked Bar Chart**: Breakdown mensile per tipologia prodotto (Indoor, Landscape, Outdoor)
- **Scatter Plot**: Matrice di segmentazione che correla Profitto Lordo % con Valore YTD per identificare account strategici

### Interattività
- **Slicer metriche**: Switch dinamico tra Vendite, Quantità e Profitto Lordo
- **Slicer temporale**: Selezione anno (2022, 2023, 2024)
- **Titoli dinamici**: Tutti i titoli di report e grafici si aggiornano automaticamente in base alle selezioni

---

## 🏗️ Architettura del Progetto

### 1. Data Modeling

**Schema a Stella** con best practice:
- **Tabelle Dimensioni** (prefisso `Dim_`):
  - `Dim_Account` - Informazioni sugli Account
  - `Dim_Products` - Anagrafica prodotti
  - `Dim_Date` - Tabella Date Creata
  
- **Tabelle Fatti** (prefisso `Fact_`):
  - `Fact_Sales` - Transazioni di vendita

- **Tabelle di supporto**:
  - `Valori_Slicer` - Tabella manuale per selezione metriche dinamiche
  - `_Measures` - Tabella dedicata alle misure calcolate

### 2. Power Query - ETL

**Trasformazioni applicate**:
- ✅ Rinomina standardizzata di tabelle e colonne
- ✅ Rimozione duplicati su `Product_ID` in `Dim_Products`
- ✅ Applicazione convenzioni di naming (Dim_ / Fact_)
- ✅ Pulizia e normalizzazione dei dati

### 3. DAX - Misure Calcolate

#### Tabella Date con logica temporale
```dax
Dim_Date = 
CALENDAR (
    DATE ( 2022, 1, 1 ),
    DATE ( 2024, 12, 31 )
)
```

#### Colonna per confronti temporali corretti
```dax
Nel_Passato = 
VAR UltimaDataVendita = MAX ( Fact_Sales[Date_Time] )
VAR UltimaDataAnnoPrecedente = EDATE ( UltimaDataVendita, -12 )
RETURN
    Dim_Date[Date] <= UltimaDataAnnoPrecedente
```

#### Misure Base
- `[Vendite]` - Totale vendite
- `[Quantita]` - Unità vendute
- `[Costo_Venduto]` - Costo del venduto
- `[Profitto_Lordo]` = [Vendite] - [Costo_Venduto]

#### Misure YTD
```dax
YTD_Vendite = 
TOTALYTD (
    [Vendite],
    Fact_Sales[Date_Time]  // Utilizzo della data dalla Fact per garantire calcoli solo su date con vendite
)
```
*Applicate anche a Quantità e Profitto Lordo*

#### Misure PYTD (Previous Year To Date)
```dax
PYTD_Vendite = 
CALCULATE (
    [Vendite],
    SAMEPERIODLASTYEAR ( Dim_Date[Date] ),
    Dim_Date[Nel_Passato] = TRUE  // Filtro per evitare confronti su periodi incompleti
)
```
*Applicate anche a Quantità e Profitto Lordo*

#### Misura Dinamica con Switch
```dax
Slicer_Valore_YTD = 
VAR ValoreSelezionato = SELECTEDVALUE(Valori_Slicer[Valori])
VAR Risultato = 
    SWITCH(
        ValoreSelezionato,
        "Vendite", [YTD_Vendite],
        "Quantità", [YTD_Quantita],
        "Profitto Lordo", [YTD_Profitto_lordo],
        BLANK()
    )
RETURN Risultato
```

#### Scostamento YoY
```dax
YTD_vs_PYTD = [Slicer_Valore_YTD] - [Slicer_Valore_PYTD]
```

#### Titoli Dinamici
```dax
_Titolo Report = 
"Plant Co. " & SELECTEDVALUE(Valori_Slicer[Valori]) & " Performance " & SELECTEDVALUE(Dim_Date[Date].[Year])

_Titolo Cascata = 
SELECTEDVALUE(Valori_Slicer[Valori]) & " YTD vs PYTD | Mese - Paese - Prodotto"
```

---

## 📁 Contenuto Repository

```
📦 Plant-Company-PowerBI-Dashboard
├── 📊 Plant_Performance.pbix          # File Power BI (SCARICA PER VISUALIZZARE)
├── 📄 Plant_DTS.xlsx                  # Dataset sorgente
├── 🖼️ screenshot_2023.png             # Preview dashboard anno 2023
└── 📖 README.md                       # Questo file
```

---

## 🚀 Come Utilizzare il Progetto

### Prerequisiti
- **Power BI Desktop** (versione più recente consigliata)
- Download gratuito: [Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)

### Istruzioni

1. **Scarica il file `.pbix`** dalla repository
2. **Apri il file** con Power BI Desktop
3. **Esplora il report** utilizzando gli slicer per:
   - Selezionare la metrica (Vendite / Quantità / Profitto Lordo)
   - Navigare tra gli anni (2022, 2023, 2024)
   - Interagire con i visual per drill-down

> ⚠️ **Nota**: Il file `.pbix` non è visualizzabile online. È necessario scaricarlo e aprirlo con Power BI Desktop per esplorare la dashboard completa.

Questo progetto è stato sviluppato a scopo dimostrativo e di portfolio.

---

⭐ **Se questo progetto ti è stato utile, lascia una stella!**
