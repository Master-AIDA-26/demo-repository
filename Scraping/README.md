# Scraper Registro Imprese — Startup e PMI Innovative

Scraper per la raccolta dei dati di startup innovative e PMI innovative pubblicati sul portale ufficiale [startup.registroimprese.it](https://startup.registroimprese.it), sviluppato nell'ambito del progetto "Startup" del corso *Big Data Processing and Data Engineering* (Master AIDA, Università Bicocca).

Il notebook `registroimprese_scraper.ipynb` esegue la ricerca sul portale, apre la scheda di dettaglio di ogni impresa e ne estrae i campi anagrafici, economici e descrittivi, salvando sia i dati strutturati (CSV) sia l'HTML grezzo di ogni pagina visitata (per poter rielaborare i dati offline in futuro, senza dover ricontattare il sito).

## Cosa fa

Il portale Registro Imprese non espone un'API pubblica: i dati vanno quindi raccolti simulando la navigazione di un utente reale (motore di ricerca, paginazione dei risultati, apertura della scheda di dettaglio di ciascuna impresa).

Per ogni impresa trovata lo scraper raccoglie:
- i **dati di card** (elenco risultati): denominazione, codice fiscale, sede, tipologia (startup/PMI innovativa), settore, data iscrizione, ecc.;
- i **dati di dettaglio** (scheda impresa): forma giuridica, capitale sociale, addetti, soci, attività svolta, presentazione, area geografica, dati di finanziamento, firma digitale e circa 15 "toggle" SI/NO (es. possesso di brevetti, spesa in R&S, compagine femminile/giovanile, ecc.).

I toggle SI/NO sono in realtà immagini renderizzate dal server (non testo HTML), quindi non possono essere letti dal solo codice sorgente della pagina: vengono invece letti a runtime, mentre il browser è aperto, tramite un piccolo script JavaScript.

## Moduli e librerie usate

| Libreria | A cosa serve |
|---|---|
| `selenium` (`webdriver`, `By`, `WebDriverWait`, `expected_conditions`) | pilota un browser Chrome reale per navigare il sito, cliccare, aspettare il caricamento delle pagine dinamiche |
| `bs4` (`BeautifulSoup`) | fa il parsing dell'HTML salvato per estrarne i campi (usato sia "a caldo" durante la navigazione, sia offline sulle pagine già salvate) |
| `csv` | lettura/scrittura dei file di output e dei file di input (parole chiave, codici fiscali) |
| `pathlib.Path` | gestione dei percorsi e delle cartelle di output in modo portabile |
| `re` | espressioni regolari, es. per estrarre il numero totale di risultati dal testo "risultato 1-20 di 2601" |
| `math` | calcolo del numero di pagine di risultati a partire dal totale |
| `time` | pause tra un'azione e l'altra, per non sovraccaricare il sito ed evitare i controlli anti-bot |
| `traceback` | log dettagliato degli errori nel file `errori_estrazione.csv`, senza interrompere lo scraping |

## Logica generale: due fasi separate

Lo scraper è organizzato in due fasi indipendenti, proprio per non dover ripetere la navigazione del sito ogni volta che si vuole correggere o migliorare l'estrazione dei dati:

**1. Raccolta (online, con Selenium)**
Il browser cerca le imprese (per parola chiave o per lista di codici fiscali), scorre le pagine di risultati e apre la scheda di dettaglio di ognuna. Per ogni impresa vengono salvati: la riga nel CSV di card, l'HTML grezzo della card e della pagina di dettaglio, e i valori dei toggle SI/NO letti in quel momento (unico dato non recuperabile dall'HTML salvato).

**2. Estrazione (offline, con BeautifulSoup)**
A partire dall'HTML già salvato sul disco (nessuna connessione al sito necessaria), i campi di dettaglio vengono ri-estratti e scritti nel CSV finale. Questa fase può essere rilanciata quante volte serve — ad esempio dopo aver migliorato la logica di estrazione di un campo — senza dover rifare lo scraping da capo.

Questa separazione è anche la principale misura anti-fragilità dello scraper: se la logica di parsing di un campo va corretta, non serve tornare sul sito.

### Dettagli tecnici principali

- **Evasione anti-bot**: il browser Chrome viene avviato in modalità headless con flag che nascondono l'automazione (`--disable-blink-features=AutomationControlled`, override via CDP di `navigator.webdriver`, user-agent personalizzato), per ridurre il rischio di blocchi.
- **Fingerprint di paginazione**: dopo il click su "pagina successiva", lo script verifica che la pagina sia effettivamente cambiata confrontando un'"impronta" del primo risultato, invece di fidarsi ciecamente del click.
- **Lettura dei toggle via JavaScript**: invece di fare uno screenshot per ognuno dei ~15 toggle di ogni impresa (lento), un unico script JS disegna ogni icona su un `<canvas>` invisibile e ne calcola il colore medio (blu = SI, grigio = NO) in un solo passaggio.
- **Ripartibilità**: prima di ogni scraping lo script carica l'elenco dei codici fiscali già raccolti e salta quelli già presenti, così un'esecuzione interrotta può essere ripresa senza duplicare i dati.
- **Retry con backoff**: se il sito risponde con una pagina di controllo anti-bot, la ricerca viene ritentata fino a 3 volte con timeout crescenti prima di segnalare un errore.
- **Riavvio periodico del browser**: ogni N imprese (parametro `RESTART_EVERY`) il browser viene chiuso e riaperto, per evitare rallentamenti o instabilità su sessioni molto lunghe.
- **Modalità di riparazione**: `ripara_dettagli_mancanti()` individua le imprese per cui la card è stata salvata ma manca il dettaglio (es. per un errore momentaneo) e riprova solo su quelle, senza rifare tutto lo scraping.
- **Errori non bloccanti**: un errore su una singola impresa viene loggato in `errori_estrazione.csv` (con traceback) e lo scraping prosegue con l'impresa successiva.

## File di output

Tutti i file vengono scritti in una cartella `data/`, creata automaticamente:

| File | Contenuto |
|---|---|
| `data/companies.csv` | dati di card (elenco risultati), un'impresa per riga |
| `data/dettagli_estratti.csv` | dati di dettaglio estratti dalle schede impresa |
| `data/toggle_flags.csv` | valori SI/NO dei toggle, letti durante la navigazione |
| `data/errori_estrazione.csv` | log degli errori incontrati, con dettaglio e traceback |
| `data/raw/` | HTML grezzo di ogni card, un file per codice fiscale |
| `data/raw_dettaglio/` | HTML grezzo di ogni scheda di dettaglio, un file per codice fiscale |

File di input (facoltativi, in base alla modalità di ricerca):
- `keywords.txt` — parole chiave da cercare
- `codici_fiscali.txt` (o un CSV esterno) — elenco di codici fiscali da cercare direttamente, senza passare dalla ricerca per parola chiave

## Come si lancia

I parametri di esecuzione si impostano in un'unica cella dedicata del notebook, prima della cella finale che lancia lo scraping vero e proprio:

- **Ricerca per parola chiave**: impostare `KEYWORDS` e lasciare `CERCA_PER_CF = False`. Lo scraping scorre tutte le pagine di risultati per ogni parola chiave.
- **Ricerca per codice fiscale**: impostare `CERCA_PER_CF = True` e fornire `LISTA_CF` (lista diretta) oppure `CF_CSV_PATH` (percorso a un CSV con una colonna di codici fiscali, indicata in `CF_CSV_COLONNA`). Più veloce perché non richiede paginazione né navigazione "indietro" tra i risultati.
- **Modalità ripara** (`RIPARA_MANCANTI = True`): ritenta solo le imprese con card salvata ma dettaglio mancante.
- **Modalità estrazione offline** (`ESTRAI_OFFLINE = True`): non apre il browser, rielabora solo l'HTML già salvato in `data/raw_dettaglio/` (utile dopo una modifica alla logica di estrazione).
- **Modalità inspect** (`INSPECT = True`): esegue una singola ricerca di prova senza salvare nulla, utile per verificare velocemente che tutto funzioni prima di un run completo.

Altri parametri utili: `MAX_PAGES` (limite di pagine per test rapidi), `NO_HEADLESS` (per vedere il browser durante il debug), `PAUSE` (pausa tra un'azione e l'altra), `RESTART_EVERY` (frequenza di riavvio del browser).

> Nota: prima di un'esecuzione completa è consigliabile provare prima "in piccolo" (poche parole chiave o `MAX_PAGES` basso, `INSPECT = True`), per verificare che la ricerca e l'estrazione funzionino correttamente sul sito nel momento in cui si lancia lo script.

## Requisiti

- Python 3
- Google Chrome installato
- Librerie: `selenium`, `beautifulsoup4`
