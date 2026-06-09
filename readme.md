# NMC Diagnostic Tool — v3.15
**Collegio Diagnostico Parallelo per Neoplasie Mieloidi Croniche (WHO-HAEM5 2022)**

Strumento di supporto decisionale per la classificazione isto-molecolare delle NMC su biopsia osteomidollare. Single-file HTML/JS, zero dipendenze, funziona offline.

---

## Cosa fa

Classifica in parallelo le quattro principali entità NMC secondo **WHO-HAEM5 2022**:

| Entità | Acronimo |
|---|---|
| Policitemia Vera | PV |
| Trombocitemia Essenziale | ET |
| Mielofibrosi Primaria Pre-fibrotica | Pre-PMF |
| Mielofibrosi Primaria Franca | PMF |

Per ciascuna produce stato diagnostico (`CONFERMATO` / `PROBABILE` / `ESCLUSO`), dettaglio criteri maggiori/minori, score di affidabilità, pannello clinico-molecolare, red flag morfologici e **referto strutturato** copiabile.

---

## Novità v3.15 — Referto per serie emopoietiche

La funzione `generateReport()` è stata riscritta per produrre un **referto in linguaggio naturale, organizzato per linea emopoietica**, al posto del precedente formato a campi etichettati con valori grezzi.

### Struttura del referto

**DESCRIZIONE MICROSCOPICA** (per punti):
- **Campione**: lunghezza, spazi intertrabecolari, qualità, cellularità complessiva contestualizzata sul range atteso per età (`getCellularityRange`)
- **Serie eritroide**: inferita da M/E, E-caderina, proliferazione
- **Serie mieloide (granulocitaria)**: inferita da proliferazione, M/E, MPO, CD15
- **Serie megacariocitaria (trombopoietica)**: morfologia megacariociti, CD61, proliferazione
- **Quota blastica**: % con interpretazione soglie + pattern MPO↑/CD15−
- **Componente linfoide**: % + distribuzione + fenotipo (CD3/CD20)
- **Plasmacellule**: CD138 % + clonalità κ/λ (policlonale / orientamento monoclonale)
- **Fibrosi reticolinica**: grading sec. consenso europeo Thiele 2005 / WHO-HAEM5 2022

**DIAGNOSI**: entità WHO primaria con stato.

**COMMENTO**: affidabilità, differenziale, dato mancante dirimente, orientamento molecolare, criteri WHO formali (collegio parallelo), score morfologici, discordanze, red flag.

### Nota epistemica

Le righe **serie eritroide** e **serie mieloide** sono *ricostruite da parametri surrogati* (M/E, E-caderina, MPO, proliferazione): inferenza, non quantificazione diretta. Le altre serie (megacariocitaria, blastica, linfoide, plasmacellulare, fibrosi) poggiano su campi diretti. Dove manca il dato → "non specificamente caratterizzata", senza fabbricazione.

### Correzione MAP_FIBROSIS

Il dizionario delle descrizioni fibrosi è stato corretto secondo **Thiele J. et al., Haematologica 2005;90(8):1128-1132 (PMID 16079113)**, adottato in WHO-HAEM5 2022:

| Grado | Definizione (consenso europeo) |
|---|---|
| MF-0 | Reticolina lineare sparsa, **senza** intersezioni (midollo normale) |
| MF-1 | Rete lassa con **molte** intersezioni, soprattutto perivascolari |
| MF-2 | Incremento diffuso e denso, estese intersezioni, ± collagene focale / osteosclerosi focale |
| MF-3 | Diffuso e denso, estese intersezioni, grossolani fasci di collagene ± osteosclerosi |

**Bug corretto**: la versione precedente descriveva MF-1 come "senza intersezioni" — errore. L'assenza di intersezioni è criterio dell'MF-0; MF-1 ha già molte intersezioni perivascolari.

> **Nota nomenclatura**: la scala MF è il consenso europeo (Thiele 2005), poi adottato dal WHO. WHO-HAEM5 è del **2022** (paper su *Leukemia*); il *blue book* cartaceo è uscito successivamente (donde la confusione "2024"). Non esiste una classificazione WHO 2024 dei mieloidi. ICC 2022 è la classificazione concorrente.

---

## Architettura logica

Invariata rispetto a v3.14. Motore WHO (`runWHOFormal`) basato solo su `data`; gate CML/MDS; MDS dimmer (`getMdsProbability`); semantica molecolare `computeHasMut` (driver / other_full / other_partial / false); `numOrNull`; XSS via `escHtml` + referto via `textContent`.

---

## Toggle UI

| Controllo | Effetto |
|---|---|
| **STRICT WHO** | Criteri più restrittivi per pre-PMF ed ET |
| **EXPERT BOARD** | Mostra dettaglio criteri espanso |

---

## Disclaimer

> Strumento di supporto decisionale per uso interno a strutture di Anatomia Patologica. Non sostituisce il giudizio clinico del patologo. La classificazione WHO-HAEM5 2022 richiede correlazione con dati clinici, laboratoristici e di imaging. Ogni diagnosi definitiva è responsabilità esclusiva del medico specialista.

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
| v3.10 | Gate CML/MDS per PV; pannello incompleto silenzioso con driver canonico positivo |
| v3.11 | `incompletoLabel` coerente con UI; `*DeclassNote` per PV |
| v3.12 | `mol_not_done` prevale su `hasValidOtherClonal`; `*DeclassNote` Pre-PMF e PMF |
| v3.13 | `etDeclassNote`; ramo morto rimosso; chip triple-neg esteso a PMF |
| v3.14 | Riga "campione inadeguato" nei details di PV/Pre-PMF/PMF; clipboard fallback |
| **v3.15** | **Referto per serie emopoietiche (eritroide → mieloide → megacariocitaria → blastica → linfoide → plasmacellule → fibrosi) + DIAGNOSI + COMMENTO; MAP_FIBROSIS corretto sec. Thiele 2005 / WHO-HAEM5 2022 (MF-1 con intersezioni); legenda UI fibrosi riallineata** |

---

## Riferimenti

- WHO Classification of Haematolymphoid Tumours, 5th ed. (WHO-HAEM5), 2022 — Khoury JD et al., *Leukemia* 2022;36:1703-1719 (PMID 35732831)
- Thiele J, Kvasnicka HM, Facchetti F, Franco V, van der Walt J, Orazi A. European consensus on grading bone marrow fibrosis and assessment of cellularity. *Haematologica* 2005;90(8):1128-1132 (PMID 16079113)

---

*Sviluppato per uso interno — ASST Fatebenefratelli-Sacco, SC Anatomia Patologica FBF-Melloni-Territorio*
