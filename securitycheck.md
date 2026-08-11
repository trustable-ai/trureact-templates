# Security check

# Role & Context
Sei un Senior Full-Stack Developer con esperienza avanzata in React, architetture serverless basate su API Nuvolaris (Apache OpenWhisk) e integrazione di modelli AI locali tramite Ollama.

# Objective
Realizza una web application in React con backend su API Nuvolaris per l'analisi automatica della sicurezza del codice sorgente tramite diversi modelli AI.

# 1. UI & Input Requirements
L'applicazione deve presentare una schermata iniziale in cui l'utente può scegliere tra due modalità di input:
* Inserire l'URL di un file di codice sorgente (es. file Raw di GitHub o GitHub Gist).
* Incollare direttamente il codice sorgente in un'area di testo.

L'interfaccia dei risultati deve essere divisa in tre sezioni, una per ogni modello AI. Ogni sezione deve contenere un pulsante con la scritta: `"Analizza con [Nome Modello]"`.

# 2. Backend Logic (Nuvolaris)
* Se l'utente fornisce un URL, il backend deve scaricare automaticamente il contenuto del file e verificarne la validità.
* Se l'utente fornisce il codice direttamente, il backend deve utilizzare quel contenuto.

# 3. AI Processing & Models (Ollama)
L'obiettivo dell'analisi è individuare vulnerabilità, problemi di sicurezza, configurazioni errate, credenziali esposte e altre criticità.
I modelli da utilizzare sono:
1.  **GLM 5.2** (https://ollama.com/library/glm-5.2)
2.  **Kimi K2.7 Code** (https://ollama.com/library/kimi-k2.7-code)
3.  **DeepSeek V4 Pro** (https://ollama.com/library/deepseek-v4-pro)
Le chiavi che devi predere dall'env sono OLLAMA_URL e OLLAMA_API_KEY

## Regole di chiamata ed Esecuzione
* **Chiamate separate:** I modelli devono essere chiamati in modo indipendente alla pressione del rispettivo pulsante, lasciandoli lavorare per tutto il tempo necessario (per evitare l'errore "response not yet ready").
* **Nessun timeout di analisi:** Rimuovi qualsiasi limite di timeout sull'esecuzione dell'analisi dell'IA; devono poter rispondere quando l'elaborazione è completata.
* **Logica di connessione e Retry:** Dai 3 minuti di tempo per stabilire la *connessione* al modello. Se fallisce, il sistema deve riprovare 4 volte (per un totale di 5 tentativi), con 3 secondi di pausa tra ogni prova.
* **Fallback:** Se al 5° tentativo la connessione fallisce (modello non disponibile), il pulsante deve aggiornarsi mostrando: `"[Nome Modello] - riprova"`.

# 4. Output & Rendering
* **Streaming & Formattazione:** I risultati restituiti dai modelli devono essere visualizzati in streaming sull'interfaccia (rilasciati man mano che si generano). L'intero output deve essere formattato e renderizzato correttamente in Markdown (non fare aree di testo raw in stile terminale).
* **Summary & Confronto Finale:** L'applicazione deve raccogliere le risposte dei tre modelli, mostrarle separatamente all'utente e generare un confronto finale che evidenzi:
    * Le vulnerabilità comuni a tutti i modelli.
    * Le vulnerabilità individuate solo da alcuni modelli specifici.
    * Un riepilogo conclusivo del livello di rischio complessivo.
