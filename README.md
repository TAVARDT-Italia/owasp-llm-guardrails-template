# OWASP LLM01 Guardrails Template & Guida alla Sicurezza dell'IA | TAVARDT Italia

[![Licence MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![OWASP LLM01](https://img.shields.io/badge/OWASP-LLM01%20Hardened-ff0055.svg)](https://owasp.org/www-project-top-10-for-large-language-model-applications/)
[![Language](https://img.shields.io/badge/Language-Italiano-green.svg)](README.md)
[![AI Diagnostics](https://img.shields.io/badge/TAVARDT-AI%20Evaluator-5b3cf5.svg)](https://tools.tavardt.com/it/valutatore-robustezza-llm/)

Blueprint dichiarativo e middleware open source per la mitigazione del Prompt Injection (OWASP LLM01), fuga di prompt di sistema e attacchi avversari su agenti di IA, conforme al RGPD e all'AI Act europeo.

Sviluppato e gestito dal team di ingegneria dell'intelligenza artificiale di **[TAVARDT Italia](https://ag.tavardt.com/it/super-intelligence/)**.

---

## 1. Panoramica dell'Architettura di Sicurezza per l'IA

Questo repository fornisce una libreria modulare e leggera per implementare **guardrail** deterministici su agenti autonomi, architetture RAG e applicazioni aziendali che utilizzano Large Language Models (OpenAI, Anthropic, Gemini, Llama, Mistral).

### La Necessità Critica di Guardrail a Livello Applicativo
I modelli di linguaggio non distinguono nativamente tra istruzioni operative di sistema e input forniti dall'utente. Senza controlli attivi in ingresso e in uscita, i sistemi basati su IA rimangono esposti a dirottamenti di contesto, furto di proprietà intellettuale e manipolazione delle risposte.

---

## 2. Vettori di Minaccia Mitigati (OWASP Top 10 for LLMs)

| Livello di Difesa | Vettore Mitigato | Meccanismo di Protezione |
| :--- | :--- | :--- |
| **Input Sanitizer** | Prompt Injection Diretto (Jailbreak) | Rilevamento euristico di comandi ostili (`ignora le istruzioni precedenti`, `DAN`, tag XML contraffatti). |
| **Canary Token Verifier** | Fuga del Prompt di Sistema | Monitoraggio di token canarino crittografici nella risposta generata per bloccare la fuoriuscita di regole interne. |
| **RAG Boundary Guard** | Prompt Injection Indiretto | Isolamento semantico dei documenti esterni recuperati all'interno della base vettoriale. |
| **Structured Output Filter** | Chiamata Non Autorizzata di Strumenti | Validazione rigorosa degli schemi JSON prima dell'attivazione di chiamate a funzioni o connettori MCP. |

---

## 3. Diagnosi Preventiva di Robustezza del Modello

Prima di mettere agenti intelligenti a contatto con clienti o processi aziendali, verificate la loro resilienza con gli strumenti di diagnosi TAVARDT:

* **Valutazione di Robustezza IA:** [Valutatore di Robustezza di LLMs TAVARDT Italia](https://tools.tavardt.com/it/valutatore-robustezza-llm/)
* **Suite Completa di Diagnostica:** [TAVARDT Tools Italia](https://tools.tavardt.com/it/)

---

## 4. Installazione e Guida all'Integrazione Rapida

### Clonazione ed Esecuzione

```bash
git clone https://github.com/TAVARDT-Italia/owasp-llm-guardrails-template.git
cd owasp-llm-guardrails-template
python examples/quickstart.py
```

### Esempio di Integrazione in Python

```python
from guardrails.validator import LLMGuardrail

guard = LLMGuardrail()

# 1. Convalida in Ingresso (Input Guardrail)
is_safe, error = guard.validate_input(user_input)
if not is_safe:
    raise ValueError(f"Input bloccato a causa di pattern ostile rilevato: {error}")

# 2. Iniezione del Token Canarino nel Prompt di Sistema
hardened_system_prompt = guard.get_canary_injection_prompt(system_prompt)

# 3. Convalida in Uscita (Output Guardrail)
is_valid, leak_error = guard.validate_output(completion)
if not is_valid:
    raise SecurityError("Allarme critico: Rilevata fuga di system prompt tramite token canarino!")
```

---

## 5. Consulenza Specialistica in Sicurezza dell'IA e Agenti Autonomi

Per le aziende che necessitano di audit di sicurezza per l'IA, protezione di agenti autonomi su WhatsApp/web o conformità all'AI Act europeo:

* **Portale Aziendale:** [TAVARDT Italia](https://ag.tavardt.com/it/super-intelligence/)
* **Canale di Contatto Tecnico:** [Richiedi Supporto Ingegneristico](https://ag.tavardt.com/it/contatti/)

---

## Licenza

Distribuito sotto licenza open-source MIT. Consultare il file [`LICENSE`](LICENSE) per maggiori informazioni.  
Copyright (c) 2026 TAVARDT Italia.
