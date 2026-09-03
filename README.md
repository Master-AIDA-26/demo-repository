<div align="center">

# 🚀 Ecosistema Startup Italia
**Data Pipeline & Business Intelligence**

> *Un'infrastruttura analitica per mappare, comprendere e valorizzare l'ecosistema dell'innovazione tecnologica italiana.*

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=for-the-badge&logo=pandas&logoColor=white)
![Status](https://img.shields.io/badge/Status-In_Sviluppo-orange?style=for-the-badge)

</div>

---

## 🎯 Scope of Work
Questo repository contiene l'intera infrastruttura di estrazione, trasformazione, integrazione (ETL) e analisi dei dati dedicata all'ecosistema delle **Startup e PMI Innovative in Italia** che hanno superato la prima validazione di mercato, registrando ricavi superiori a **400.000 euro**.

L'obiettivo del progetto è superare la frammentazione informativa delle fonti pubbliche e private, offrendo un quadro analitico e strategico a supporto di:
* **Venture Capital, Angel Network e Investitori Istituzionali:** Per identificare trend settoriali, valutare l'efficacia della raccolta capitali e analizzare le dinamiche reali di co-investimento.
* **Enti Pubblici:** Per promuovere l'attrattività del territorio verso fondi esteri (FDI) e monitorare l'impatto delle policy sull'innovazione.


## 📊 Pilastri Analitici e Risultati di Business
Il progetto analizza l'ecosistema italiano focalizzandosi su tre dimensioni chiave:

1. ⏳ **Profilo Temporale e Demografico:** Tracciamento dei tassi di costituzione delle startup e della loro distribuzione settoriale.
2. 📈 **Sostenibilità e Raccolta Capitali (Bootstrapping vs VC):** Tracciamento dei parametri economico-finanziari per mappare il percorso delle aziende verso il *Breakeven Point* e la transizione da Startup a Scale-up.
3. 🕸 **Network Analysis dei Co-Investimenti:** Analisi testuale avanzata per categorizzare le tecnologie proprietarie, i servizi offerti e le sinergie di filiera.

---

## 🏗️ Architettura Tecnologica e Flusso ETL
Il sistema implementa una pipeline dati robusta e replicabile, sviluppata al **100% in Jupyter Notebooks**, per raccogliere, pulire e unire i dati amministrativi e finanziari di due fonti principali:

* **Registro delle Imprese (Sezione Speciale):** Dati anagrafici e descrittivi di 659 startup innovative estratti tramite scraping.
* **Database AIDA (Bureau van Dijk):** Storico finanziario (2020-2024), ricavi, capitale sociale e numero di dipendenti per 715 aziende con ricavi oltre i 400k€ in almeno un anno di bilancio disponibile nell'arco temporale 2020-2024.


### Fasi della Data Pipeline (ETL)
1. **Extraction (Scraping Resiliente):** Lo scraping del Registro delle Imprese separa nettamente le responsabilità tra **Selenium** (apertura dinamica della ricerca, gestione della paginazione, cattura dei toggle grafici dell'innovazione e salvataggio dell'HTML grezzo in locale) e **BeautifulSoup** (parsing offline dei file scaricati), azzerando le richieste ripetute al server.
2. **Data Cleaning & Enrichment:** Rimozione di 24 colonne ridondanti (scendendo da 91 a 67 colonne pulite). Esecuzione dello split geografico (comuni e province), decodifica dei codici ATECO ed estrazione di variabili binarie (0/1) tramite text mining delle parole chiave nei pitch (es. presenza di incubatori, università o investitori), salendo a 78 colonne complessive.
3. **Merge e Validazione (Data Quality):** Rimozione dei testi liberi e unione (inner join) sul Codice Fiscale con le metriche finanziarie di AIDA (35 colonne utili). Il dataset finale conta **103 variabili pulite** per un campione integrato di **659 aziende operative**. La qualità dei dati è certificata da un test automatico che applica una soglia di tolleranza di coincidenza $\ge$95% sulle colonne pulite per escludere record duplicati.
4. **Pipeline GenAI (Estrazione dei Round):** I round di investimento storici sono estratti da fonti web non strutturate. Un agente basato su **Tavily API** raccoglie articoli di giornale e comunicati stampa online, un **LLM OpenAI** estrae in modo strutturato le informazioni del deal (data, importo, tipo di round, investitori coinvolti) e un algoritmo di **Fuzzy Matching** effettua l'Entity Resolution per risolvere omonimie e riconciliare i nomi dei fondi.

---

## 💻 Organizzazione del Repository

Il codice è strutturato in cartelle logiche che rispecchiano le fasi della pipeline:

* 📁 `/Scraping`: Codici per l'automazione del browser (Selenium) e l'estrazione offline del testo (BeautifulSoup).
* 📁 `/LLM`: Prompt, schemi e parser JSON per la categorizzazione dei pitch aziendali tramite modelli generativi.
* 📁 `/Estrazione storico round investimenti`: Pipeline end-to-end con Tavily API e OpenAI LLM per la raccolta e l'estrazione strutturata delle notizie sui round, comprensiva di algoritmi di Fuzzy Matching.
* 📁 `/Analisi investimenti`: Elaborazione, calcolo dei KPI economico-finanziari, query SQL e preparazione dei dati per la modellazione di rete.
* 📁 `/visualizzazione grafica analisi`: Generazione della mappa dei co-investimenti, istogrammi dimensionali, trend temporali e grafici distributivi.

---

## 💻 Quick Start (Per i Membri del Team)

Per contribuire allo sviluppo della pipeline in ambiente locale:

**1. Clonare il repository**
```bash
git clone [https://github.com/TuaOrganizzazione/ecosistema-startup-ita.git](https://github.com/TuaOrganizzazione/ecosistema-startup-ita.git)
cd ecosistema-startup-ita
