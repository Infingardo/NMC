# NMC Diagnostic Tool — v3.14
**Collegio Diagnostico Parallelo per Neoplasie Mieloidi Croniche (WHO 2022)**

Strumento di supporto decisionale per la classificazione isto-molecolare delle NMC su biopsia osteomidollare. Single-file HTML/JS, zero dipendenze, funziona offline.

---

## Cosa fa

Classifica in parallelo le quattro principali entità NMC secondo **WHO 2022**:

| Entità | Acronimo |
|---|---|
| Policitemia Vera | PV |
| Trombocitemia Essenziale | ET |
| Mielofibrosi Primaria Pre-fibrotica | Pre-PMF |
| Mielofibrosi Primaria Franca | PMF |

Per ciascuna produce:
- **Stato diagnostico**: `CONFERMATO` / `PROBABILE` / `ESCLUSO`
- **Dettaglio criteri maggiori e minori** con semaforo (verde / giallo / rosso / grigio = non valutabile)
- **Score di affidabilità** (0–100) con penalità granulate per dati mancanti
- **Pannello clinico-molecolare** con bias d'entità per ciascun parametro
- **Red flag morfologici** (blasti, leucoeritroblastosi, monocitosi, discordanze)
- **Referto strutturato** copiabile in clipboard

---

## Architettura logica

### Motore WHO (`runWHOFormal`)

Calcola i criteri per ciascuna entità usando solo `data` (nessun accesso diretto al DOM). Gestisce:

- **Gate CML/MDS**: `t(9;22)` esclude tutte le NMC; `MDS-likely` declassa a PROBABILE; `MDS-possible` aggiunge criterio parziale. Valido per tutte e quattro le entità (PV inclusa dalla v3.10).
- **Gate campione inadeguato**: declassa CONFERMATO → PROBABILE con nota esplicita nei dettagli e nel summary.
- **`*DeclassNote`**: ogni entità traccia il motivo della declassazione nel summary (`pvDeclassNote`, `etDeclassNote`, `preDeclassNote`, `pmfDeclassNote`).
- **Strict mode**: toggled in UI; pre-PMF richiede cellularità aumentata obbligatoria + ≥1 tra granulocitosi/eritroide ridotta; ET richiede proliferazione megacariocitaria.

### Semantica molecolare

| Valore `computeHasMut()` | Significato |
|---|---|
| `'driver'` | JAK2 / CALR / MPL positivo |
| `'other_full'` | Altro clonale valido + tutti i driver testati |
| `'other_partial'` | Altro clonale valido + pannello driver incompleto |
| `false` | Nessuna clonalità documentata |

**Priorità logica nel pannello driver:**
1. `mol_not_done` → "NON ESEGUITO" (sempre, indipendentemente da altro clonale)
2. `driver_panel_incomplete && !canonicalDriver` → "INCOMPLETO" con pos/neg/nd distinti
3. `triple_neg` → "TRIPLO-NEG (driver testati)"
4. driver positivo → label con nome mutazione

La regola chiave: **altro clonale non sopprime il warning sui driver canonici non eseguiti** (dalla v3.12).

### `numOrNull(id)`

Tutti i campi numerici usano questa helper. `''` → `null`; qualsiasi numero incluso `0` → `Number`. Evita il classico `parseFloat() || null` che mangia gli zeri.

### `hasValidOtherClonal(data)`

Gate unico per "altro marcatore clonale valido": richiede checkbox + testo non vuoto. Usato in tutti i punti logici; non usato come condizione per sopprimere warning sui driver mancanti.

---

## Pannello clinico-molecolare (`runClinMol`)

Genera item con struttura:

```
{ label, value, type: 'pos'|'partial'|'neg', bias, note }
```

Mostra bias d'entità per ogni parametro (es. PLT ≥800 → "ET trombocitosi estrema"). Separato dal motore WHO: può essere consultato indipendentemente.

---

## Score di affidabilità (`computeConfidence`)

Penalità principali:

| Condizione | Penalità |
|---|---|
| Campione inadeguato | −35 |
| Fibrosi non valutata | −20 |
| Molecolare non eseguito | −20 |
| Emocromo assente | −15 |
| Campione subottimale | −15 |
| Pannello driver incompleto | −12 |
| Clonalità alternativa parziale | −10 |
| Discordanza WHO/morfo | −10 |
| CALR + PV | −15 |
| Multipli PROBABILE | −10 × (n−1) |
| MDS-likely | −10 |

---

## MDS dimmer (`getMdsProbability`)

| Evento | Punti |
|---|---|
| Displasia multilineare | +3 |
| Blasti 10–19% | +3 |
| Blasti 5–9% | +2 |
| del(5q) / −7 / complesso | +2 |
| Trisomia 8 / del(20q) | +1 (aspecifici) |
| Blasti 2–4% | +1 |

Score ≥5 → `likely` (blocca esclusione WHO); 2–4 → `possible` (rende criterio parziale); <2 → `not_supported`.

---

## Toggle UI

| Controllo | Effetto |
|---|---|
| **STRICT WHO** | Criteri più restrittivi per pre-PMF ed ET |
| **EXPERT BOARD** | Mostra dettaglio criteri espanso |

---

## Chip informativi

- **Pre-PMF**: entità WHO 2022, non formalizzata in ICC 2022
- **Triple-neg**: ICC 2022 richiede clonalità alternativa documentata (compare per ET, Pre-PMF e PMF)
- **PV/MDS**: avviso se contesto MDS-likely con criteri PV soddisfatti

---

## Referto strutturato

Generato da `generateReport()` via `textContent` (XSS-safe). Include:

- Dati biopsia (qualità, lunghezza, spazi, fibrosi)
- Dati clinici (sesso, età, emocromo)
- Stato molecolare con dettaglio pannello incompleto (positivi / negativi / non eseguiti)
- Classificazione WHO 2022 con score affidabilità e motivazione declassazione
- Red flag e discordanze
- Dati mancanti per completamento

Copiabile con bottone **⎘ Copia** — fallback `execCommand` per contesti `file://` e browser senza Clipboard API.

---

## Sicurezza

- **XSS**: `escHtml()` su tutte le stringhe utente in innerHTML; referto via `textContent`
- **Separazione model/UI**: zero `getElementById` nelle funzioni classificatorie (`runWHOFormal`, `computeHasMut`, `computeConfidence`, ecc.)
- **Nessuna dipendenza esterna**: funziona offline, nessuna telemetria

---

## Disclaimer

> Strumento di supporto decisionale per uso interno a strutture di Anatomia Patologica. Non sostituisce il giudizio clinico del patologo. La classificazione WHO 2022 richiede correlazione con dati clinici, laboratoristici e di imaging. Ogni diagnosi definitiva è responsabilità esclusiva del medico specialista.

---

## Cronologia versioni (sintesi)

| Versione | Contributo principale |
|---|---|
| v3.1 | Base: motore WHO 2022, pannello morfo, confidence score |
| v3.2 | `getWHOPrimary` con subscore; MDS dimmer; autoBiopsyQuality |
| v3.3 | XSS sistematico; semantica `computeHasMut` full/partial |
| v3.4 | Separazione model/UI completa |
| v3.5 | `driver_panel_incomplete` end-to-end |
| v3.6 | Motore WHO: pannello incompleto ≠ negativo in ET/Pre-PMF/PMF/PV |
| v3.7 | `numOrNull`; check truthy numerici → `!== null`; chip ICC corretto |
| v3.8 | Pannello incompleto: pos/neg/nd distinti; report allineato alla UI |
| v3.9 | Rimozione `allDriversTestedNegative`; fix `pct !== null`; `len !== null` |
| v3.10 | Gate CML/MDS per PV; pannello incompleto silenzioso con driver canonico positivo; `lymphoid_percent` semanticamente pulito |
| v3.11 | `incompletoLabel` coerente con UI; `*DeclassNote` per PV + simmetria editoriale |
| v3.12 | `mol_not_done` prevale su `hasValidOtherClonal`; `*DeclassNote` per Pre-PMF e PMF |
| v3.13 | `etDeclassNote`; ramo morto rimosso; chip triple-neg esteso a PMF |
| v3.14 | Riga "campione inadeguato" nei details di PV/Pre-PMF/PMF; clipboard fallback |

---

*Sviluppato per uso interno — ASST Fatebenefratelli-Sacco, SC Anatomia Patologica FBF-Melloni-Territorio*
