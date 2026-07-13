<div align="center">

# 🚀 Ecosistema Startup Italia
**Data Pipeline & Business Intelligence**

> *Un'infrastruttura analitica per mappare, comprendere e valorizzare l'ecosistema dell'innovazione tecnologica italiana.*

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=for-the-badge&logo=pandas&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-4EA94B?style=for-the-badge&logo=mongodb&logoColor=white)
![Neo4j](https://img.shields.io/badge/Neo4j-018BFF?style=for-the-badge&logo=neo4j&logoColor=white)
![Status](https://img.shields.io/badge/Status-In_Sviluppo-orange?style=for-the-badge)

</div>

---

## 🎯 Scope of Work
Questo repository contiene l'infrastruttura di estrazione, trasformazione e analisi (ETL) dedicata all'ecosistema delle **Startup e PMI Innovative in Italia**. 

L'obiettivo principale è superare la frammentazione dei dati pubblici e privati, fornendo insight strategici basati sui dati per supportare le decisioni di:
* **Investitori Istituzionali (Venture Capital & Business Angels):** Per valutare i trend di mercato, i settori a più alta trazione e i network relazionali di successo.
* **Pubblica Amministrazione (es. Enti Regionali):** Per promuovere l'attrattività del territorio verso fondi esteri (FDI) e monitorare l'impatto delle policy sull'innovazione.

## 📊 Pilastri Analitici
Il progetto è strutturato per rispondere a 4 macro-aree di business:

1. ⏳ **Longevità e Ciclo di Vita:** Analisi dei tassi di sopravvivenza e individuazione dei settori merceologici più resilienti.
2. 📈 **Sostenibilità ed Evoluzione:** Tracciamento dei parametri economico-finanziari per mappare il percorso delle aziende verso il *Breakeven Point* e la transizione da Startup a Scale-up.
3. 🕸️ **Network Relazionale:** Modellazione tramite database a grafo delle connessioni tra *founder*, *board member* e società (es. impatto dei *serial founder* e spin-off).
4. 🏭 **Ecosistema Commerciale:** Analisi testuale avanzata per categorizzare le tecnologie proprietarie, i servizi offerti e le sinergie di filiera.

---

## 🏗️ Architettura e Sorgenti
Il sistema integra diverse fonti dati per creare un *Golden Record* (profilo univoco validato) per ogni entità aziendale.

* **Dati Governativi / Open Data:** Utilizzati per validare lo status giuridico attuale (iscrizione a sezioni speciali), la demografia e la localizzazione geografica.
* **Provider Dati Finanziari:** Utilizzati per l'ingestione massiva di dati anagrafici storici, metriche di bilancio e composizione societaria.

### Flusso Dati (Data Pipeline)
1. **Extraction & Cross-check:** Ingestione asincrona dalle fonti e quadratura basata su identificativi univoci (Partita IVA) per individuare lo status reale delle aziende.
2. **Data Cleaning & Enrichment:** Normalizzazione anagrafica, estrazione di concetti chiave tramite Text Mining e calcolo dei KPI.
3. **Storage Multimodello:**
   - *Documentale (MongoDB):* Per la conservazione flessibile dei metadati aziendali, anagrafiche e storici di bilancio.
   - *A Grafo (Neo4j):* Dedicato esclusivamente al routing e all'esplorazione delle connessioni tra persone fisiche e persone giuridiche.

---

## 💻 Quick Start (Per i Membri del Team)

Per contribuire allo sviluppo della pipeline in ambiente locale:

**1. Clonare il repository**
```bash
git clone [https://github.com/TuaOrganizzazione/ecosistema-startup-ita.git](https://github.com/TuaOrganizzazione/ecosistema-startup-ita.git)
cd ecosistema-startup-ita