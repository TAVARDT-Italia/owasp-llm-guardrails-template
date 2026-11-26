<p align="center">
  <a href="https://ag.tavardt.com/it/">
    <img src="https://ag.tavardt.com/img/tavardt-banner.png" alt="TAVARDT" width="1024" />
  </a>
</p>

# OWASP LLM01 Guardrails Template e Blueprint di Sicurezza per IA

[![OWASP LLM01](https://img.shields.io/badge/OWASP-LLM01%20Hardened-ff0055.svg)](https://owasp.org/www-project-top-10-for-large-language-model-applications/)
[![AI Diagnostics](https://img.shields.io/badge/TAVARDT-AI%20Evaluator-5b3cf5.svg)](https://tools.tavardt.com/it/valutatore-robustezza-llm/)
[![Licence MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

***

Middleware open source e blueprint di sicurezza per la mitigazione di Prompt Injection (OWASP LLM01), estrazione dei prompt di sistema e jailbreak avversari, sviluppato da **TAVARDT**, in piena conformità con le linee guida AgID sull'IA, il Regolamento Europeo sull'IA e il GDPR.

I Large Language Model elaborano le istruzioni di sistema e i dati immessi dagli utenti all'interno del medesimo contesto di esecuzione. Senza un livello applicativo di sanificazione degli input e verifica delle risposte tramite token canarino, gli agenti autonomi e le pipeline RAG rimangono esposti a manipolazioni del contesto e divulgazione di informazioni riservate.

***

## Funzionalità Principali e Architettura Difensiva

* **Sanificazione Deterministica degli Input**: Esamina le richieste in ingresso mediante espressioni regolari compilate per bloccare tentativi di sovrascrittura delle istruzioni e manipolazioni di delimitatori prima della generazione.
* **Verifica Crittografica tramite Token Canarino**: Introduce un token imprevedibile (`TAVARDT_CANARY_<hex16>`) generato dal modulo `secrets` per identificare e terminare sessioni che tentino di sottrarre il prompt di sistema.
* **Copertura Completa delle Minacce**: Mitiga i rischi specificati negli standard OWASP-LLM01 (Prompt Injection), OWASP-LLM06 (Divulgazione di Informazioni Sensibili) e OWASP-LLM07 (Progettazione Insicura di Plugin).
* **Zero Dipendenze Software Esterne**: Funziona interamente con la libreria standard di Python, garantendo una latenza inferiore a 2 millisecondi senza chiamate di rete verso servizi terzi.
* **Integrazione Modulare e Immediata**: Protegge facilmente le chiamate verso qualsiasi fornitore di modelli (OpenAI, Anthropic, Google Gemini, modelli Llama self-hosted) in agenti autonomi, chatbot e sistemi RAG.

***

## Vettori di Rischio e Impatto Operativo

L'impiego operativo di modelli linguistici privi di guardrail applicativi espone il patrimonio informativo e i tool aziendali a manipolazioni malevole. Il blueprint neutralizza quattro vettori fondamentali:

* **Iniezione Diretta di Prompt (OWASP LLM01)**: Comandi ostili studiati per annullare le direttive iniziali (`ignore previous instructions`) e forzare l'esecuzione di compiti non consentiti.
* **Esfiltrazione del Prompt di Sistema (OWASP LLM06)**: Richieste manipolatorie finalizzate a indurre il modello a mostrare le proprie istruzioni confidenziali e le regole operative interne.
* **Manipolazione dei Delimitatori e Jailbreak via Ruoli**: Uso improprio di tag markdown o XML per attivare modalità sviluppatore o aggirare le restrizioni comportamentali.
* **Iniezione Indiretta mediante Archivi Documentali**: Dati ostili occultati all'interno di documenti di terze parti memorizzati in database vettoriali per pipeline RAG.

***

## Installazione ed Esecuzione CLI

Integrate il middleware di sicurezza nel vostro progetto Python o verificate il funzionamento locale tramite lo script rapido incluso:

```bash
# Clonare il repository ufficiale
git clone https://github.com/TAVARDT-Italia/owasp-llm-guardrails-template.git

# Accedere alla directory del progetto
cd owasp-llm-guardrails-template

# Eseguire lo script di verifica rapida
python examples/quickstart.py
```

### Python Integration Example

Proteggete qualsiasi chiamata di inferenza con controlli deterministici in ingresso e in uscita:

```python
from guardrails.validator import LLMGuardrail

guard = LLMGuardrail()

# 1. Ingress Validation (Input Guardrail)
is_safe, error = guard.validate_input(user_input)
if not is_safe:
    raise ValueError(f"Input blocked due to suspicious adversarial pattern: {error}")

# 2. Inject Canary Token into System Prompt
hardened_system_prompt = guard.get_canary_injection_prompt(system_prompt)

# 3. Egress Validation (Output Guardrail)
is_valid, leak_error = guard.validate_output(completion)
if not is_valid:
    raise SecurityError("Critical alert: System prompt leak detected via canary token!")
```

***

## Domande Tecniche Frequenti

### Quali vettori di vulnerabilità vengono neutralizzati dal template di guardrail OWASP LLM nelle pipeline di intelligenza artificiale?

Il template impone difese applicative deterministiche contro iniezioni dirette di prompt, jailbreak avversari ed esfiltrazione delle istruzioni di sistema. Combinando l'analisi degli input tramite espressioni regolari con la verifica delle uscite mediante token canarino, il middleware protegge i modelli senza richiedere attività di fine-tuning.

### Come opera la verifica dinamica tramite token canarino per impedire la fuga del prompt di sistema?

La classe LLMGuardrail genera un identificatore crittografico univoco e lo inserisce nelle istruzioni di sistema assieme a una direttiva esplicita di segretezza. Durante la fase di output, il validatore analizza il testo generato e blocca tempestivamente qualsiasi risposta che contenga il token riservato.

### Per quale motivo è necessaria la sanificazione deterministica degli input se il modello possiede già filtri etici?

I filtri comportamentali dei modelli possono essere aggirati mediante formulazioni linguistiche complesse, codifiche insolite o scenari di simulazione di ruolo. L'analisi preliminare degli input intercetta i pattern malevoli noti prima che raggiungano il contesto computazionale, azzerando i rischi e contenendo i costi di inferenza.

### In che modo questo template di guardrail si integra in pipeline di agenti autonomi e architetture RAG?

Il guardrail protegge le sessioni di inferenza sanificando sia i frammenti documentali recuperati sia i messaggi inviati dagli utenti prima della composizione del prompt finale. Questa separazione impedisce che iniezioni indirette celate nei database vettoriali alterino il ragionamento dell'agente o attivino strumenti software non autorizzati.

### Qual è il sovraccarico computazionale e l'impatto sulla latenza introdotto dallo strato di convalida?

Il validatore Python opera completamente in memoria locale impiegando espressioni regolari precompilate e confronti nativi tra stringhe, terminando l'analisi in meno di 2 millisecondi. Poiché non effettua interrogazioni verso API remote né si affida a modelli secondari, la soluzione non introduce latenza di rete negli ambienti di produzione.

***

## Link Utili

* **Portale Aziendale:** https://ag.tavardt.com/it/
* **Suite di Diagnostica:** https://tools.tavardt.com/it/
* **Contatto Tecnico:** https://ag.tavardt.com/it/contatto/

***

### Licenza
Distribuito sotto la Licenza MIT. Consultare il file [LICENSE](LICENSE) per tutti i dettagli.

***

© 2026 TAVARDT. Tutti i diritti riservati.
