# 🔬 NMC Diagnostic Tool v2.0 - Pattern Recognition + WHO 2022

Workflow diagnostico completo per Neoplasie Mieloproliferative Croniche (MPN) con **doppio sistema di valutazione**.

## ✨ Novità v2.0

### Sistema Diagnostico Doppio Binario

**1. Pattern Recognition (Scoring Euristico)**
- Analisi rapida basata su pattern istologici
- Score 0-100 per ET, PV, PMF, Pre-PMF
- Utile per screening iniziale e orientamento diagnostico

**2. Criteri WHO 2022 Formali** ⭐ NUOVO
- Applicazione rigorosa dei criteri ufficiali WHO 2022
- Sistema maggiori/minori per PV
- 4 criteri obbligatori per ET
- Criteri maggiori + minori per PMF
- **Output: CONFERMATO / NON CONFERMATO / ESCLUSO**

### Confronto Output

```
ESEMPIO OUTPUT:

📊 Criteri WHO 2022 Formali
━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Policitemia Vera - PV_CONFERMATO
   Maggiori: 3/3, Minori: 0/1
   ✓ Maggiore 1: Hb 17.2 g/dL (>16.5)
   ✓ Maggiore 2: Panmielosi + megacariociti pleomorfi
   ✓ Maggiore 3: JAK2 V617F positivo

━━━━━━━━━━━━━━━━━━━━━━━━━━
🔬 Pattern Recognition
   Score: 85/100 (Confidenza: 85%)
   Supporta diagnosi PV
```

## 📋 Caratteristiche Complete

### Input Dati
- **Dati paziente**: età, sesso (per soglie PV age/sex-adjusted)
- **Istologia completa**:
  - Cellularità WHO 2022 (range age-adjusted automatici)
  - Morfologia megacariociti (4 pattern: ET, PV, PMF, normale)
  - Proliferazione midollare (5 pattern)
  - Fibrosi MF-0/1/2/3
  - Rapporto M/E
  - Componente linfoide (% e distribuzione)

- **Pannello IHC completo**:
  - **Linea eritroide**: E-caderina (CRITICO per PV)
  - **Linea granulocitaria**: MPO, CD15
  - **Linea monocitica**: CD68 (KP1/PG-M1), CD14, CD163
  - **Linea megacariocitica**: CD61
  - **Linea linfoide**: CD3, CD20
  - **Plasmacellule**: CD138, K/L

- **Dati clinico-laboratoristici**:
  - Emocromo (Hb, Hct, PLT, WBC) → per criteri WHO
  - Blasti% → AML exclusion
  - Monociti assoluti → LMMC diagnosis
  - Mutazioni JAK2/CALR/MPL
  - Citogenetica (con CML t(9;22) detection)
  - Segni clinici: splenomegalia, sintomi, anemia, LDH

### Output Diagnostico

#### 1. Red Flags (Priorità Massima)
- 🚨 **AML**: blasti >20%, pattern MPO/CD15, CD68/CD14 monoblastico
- 🚨 **LMMC**: CD68++/CD14++/CD163++ + monociti >1×10⁹/L
- 🚨 **Linfoma**: componente linfoide >20%, pattern a tappeto
- 🚨 **Mieloma**: CD138 >10% ± K/L alterato
- 🚨 **CML**: t(9;22) BCR/ABL

#### 2. Criteri WHO 2022 (NUOVO)

**Policitemia Vera**
- Diagnosi: ≥2 maggiori O 1 maggiore + 1 minore
- Valutazione automatica soglie Hb/Hct sex-adjusted
- Check panmielosi + megacariociti pleomorfi
- Integrazione JAK2

**Trombocitemia Essenziale**
- Diagnosi: TUTTI i 4 criteri
- PLT ≥450, megacariociti iperlobulati
- Esclusione PV/PMF/CML/MDS automatica
- Driver mutation required

**Mielofibrosi Primaria**
- Diagnosi: ≥3 maggiori + ≥1 minore
- Clustering + fibrosi MF-2/3
- Score minori: anemia, leucocitosi, splenomegalia, LDH
- Distinzione Pre-PMF automatica

#### 3. Pattern Recognition
- Score 0-100 per ET/PV/PMF/Pre-PMF
- Ranking automatico con diagnosi differenziale
- Reasoning esplicito (punti positivi/negativi)

#### 4. Referto Strutturato
- Sezioni: cellularità, istologia, IHC, dati clinici
- Diagnosi WHO + Pattern recognition integrati
- Formato copy-paste per refertazione

## 🎯 Uso Clinico

### Workflow Consigliato

1. **Compila dati obbligatori**:
   - Cellularità, megacariociti, proliferazione, fibrosi
   - Almeno emocromo base (Hb/Hct/PLT/WBC)

2. **Aggiungi IHC disponibile**:
   - Minimo: E-caderina, CD61, CD68
   - Completo: pannello full

3. **Analizza caso** → Output doppio:
   - ✅ **WHO 2022**: diagnosi formale (CONFERMATO/ESCLUSO)
   - 🔬 **Pattern Recognition**: supporto diagnostico

4. **Interpreta risultati**:
   - Se WHO confermato → diagnosi certa
   - Se WHO non confermato ma pattern high score → rivaluta dati/IHC
   - Se red flag critico → priorità assoluta

### Esempio Caso Completo

```
Input:
- M, 62 anni
- Hb 17.8, Hct 52%, PLT 580, WBC 14
- Panmielosi, megacariociti pleomorfi
- E-caderina aumentata, M/E 1.5:1
- JAK2 V617F+
- Fibrosi MF-0

Output WHO 2022:
✅ PV_CONFERMATO (3/3 maggiori)

Pattern Recognition:
Score PV: 90/100

Diagnosi: POLICITEMIA VERA WHO 2022
```

## 🚨 Limitazioni

⚠️ **Il tool NON sostituisce la diagnosi clinica**
- È un supporto per pattern recognition e applicazione criteri
- La diagnosi finale richiede correlazione clinico-patologica
- Alcuni criteri WHO (EPO, leucoeritroblastosi) non valutabili
- Red flags richiedono conferma con indagini aggiuntive

## 📚 Riferimenti

- WHO Classification of Haematolymphoid Tumours 2022 (5th edition)
- Criteri diagnostici aggiornati per PV, ET, PMF
- Range cellularità age-adjusted (0-120 anni)
- Pattern IHC validati su pannello reale di laboratorio

## 🔄 Versioni

**v2.0** (corrente)
- ✨ Criteri WHO 2022 formali integrati
- Sistema diagnostico doppio binario
- Output WHO CONFERMATO/ESCLUSO
- LDH e anemia per PMF scoring

**v1.0** 
- Pattern recognition istologico
- Red flags detection
- Pannello IHC completo
- Cellularità WHO 2022

## 💾 Salvataggio Dati

- **localStorage browser**: i dati vengono salvati localmente
- Nessun server, privacy totale
- Reset manuale disponibile

## 🔧 Deploy

```bash
# Copia index_v2.html in root repo
# Rinomina in index.html
# Push su GitHub
# Abilita GitHub Pages
```

---

**Developed for SC Anatomia Patologica**  
ASST Fatebenefratelli-Sacco, Milano

Tool diagnostico per uso professionale in anatomia patologica.
