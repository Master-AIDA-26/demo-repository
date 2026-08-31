FLUSSO DI LAVORO

OBIETTIVO >>> estrarre in modo strutturato eventi sulla compagine sociale di startup italiane (round di investimenti, cessioni di quote, ecc) a partire da articoli di stampa.

DATASET >>> il dataset di input è l'elenco di startup estratto da AIDA contenente l'anagrafica delle aziende target.

OUTPUT >>> un file con gli eventi estratti 

COME FUNZIONA IL CODICE

STEP 0

- qui definiamo i parametri globali: chiavi API, LLM utilizzato, percorsi di input e output, le query che diamo in input a Tavily, soglie. 
- puliamo il nome delle aziende da suffissi legali tramite regex
- per ogni azienda costruiamo un dizionare con nome pulito, ragione sociale originale, codice fiscale, website, ATECO, stato giuridico
- le aziende già processate vengono esclude da batch di esecuzione corrente, per evitare di ripetere chiamate API già eseguite

STEP 1

- per ogni aziende vengono lanciate due query distinte per estrarre eventi primari ed eventi secondari
- ogni richiesta Tavily recupera il testo integrale degli articoli
- i risultati vengono poi deduplicati per URL e filtrati per lunghezza così da scartare pagine di errore o contenuti troppo brevi
- l'output qui è una lista di dizionari, ciascuno contenente nome azienda, ragione sociale, codice fiscale, URL articolo, titolo articolo, data di pubblicazione articolo e testo integrale articolo

STEP 2:

- ogni articolo viene inviato a gpt-4o-mini con un system prompt che definisce il compito dell'LLM e le regole operative
- uno user message include il nome dell'azienda target e il testo dell'articolo
- forziamo il modello a darci un oggetto json in output con campi predefiniti e strutturati 
- gli eventi estratti vengono arricchiti con i metadati della fonte (URL, data, testo) e raccolti in una lista 

STEP 3

- per ogni evento estratto eseguiamo due check tramite fuzzy matching: 
    1) verifichiamo le citazioni, ogni stringa nel campo 'evidence' viene confrontata con il testo originale dell'articolo (una citazione è considerata trovata se il punteggio supera 90)
    2) verifichiamo i nomi delle, ogni nome di investitore o socio uscente viene cercato nel testo con la stessa soglia
- in base all'esito dei due controlli un'evento viene classificato come verificato, sospetto o scartato

STEP 4

- eventi_estratti.xlsx >>> tabella con colonne principali (azienda, ragione sociale, codice fiscale, data, tipo evento, tipo round, importo in euro, investitori, soci uscenti, confidence, verifica, evidence, URL fonte)
- eventi_estratti.json >>> versione json di quella su
- progresso.json >>> lista dei nomi delle aziende già processate


