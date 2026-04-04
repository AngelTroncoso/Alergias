# 🧬 AllerCure-LATAM
### *Pipeline computacional hacia la cura de alergias a ácaros del polvo doméstico en Latinoamérica*

<p align="center">
  <img src="https://img.shields.io/badge/estado-en%20desarrollo-yellow?style=for-the-badge" />
  <img src="https://img.shields.io/badge/python-3.10%2B-blue?style=for-the-badge&logo=python" />
  <img src="https://img.shields.io/badge/plataforma-Google%20Colab-orange?style=for-the-badge&logo=googlecolab" />
  <img src="https://img.shields.io/badge/licencia-MIT-green?style=for-the-badge" />
  <img src="https://img.shields.io/badge/contexto-Hackathon%20LATAM-red?style=for-the-badge" />
</p>

---

> **Misión:** Transformar datos moleculares y clínicos del reporte traslacional sobre *Dermatophagoides* en una herramienta computacional de decisión en tiempo real, adaptada a la realidad epidemiológica y genómica de América Latina.

---

## 📋 Tabla de contenidos

- [Contexto científico](#-contexto-científico)
- [Arquitectura del pipeline](#-arquitectura-del-pipeline)
- [Fases del proyecto](#-fases-del-proyecto)
- [Instalación](#-instalación)
- [Uso rápido](#-uso-rápido)
- [Estructura del repositorio](#-estructura-del-repositorio)
- [Biomarcadores clave](#-biomarcadores-clave)
- [Modelo predictivo](#-modelo-predictivo)
- [Consideraciones LATAM](#-consideraciones-latam)
- [Roadmap](#-roadmap)
- [Contribuir](#-contribuir)
- [Citación](#-citación)
- [Equipo](#-equipo)

---

## 🔬 Contexto científico

La hipersensibilidad alérgica a los ácaros del polvo doméstico (*Dermatophagoides pteronyssinus* y *D. farinae*) afecta a más de **400 millones de personas** en el mundo, con una prevalencia desproporcionadamente alta en climas tropicales y subtropicales de Latinoamérica.

El objetivo de este repositorio **no es el manejo sintomático**, sino avanzar hacia una **cura inmunológica definitiva** mediante:

| Nivel de intervención | Objetivo | Estado en LATAM |
|---|---|---|
| 🔴 Tratamiento sintomático | Control de manifestaciones | Ampliamente disponible |
| 🟡 Modificación de enfermedad (AIT) | Tolerancia duradera | Acceso desigual |
| 🟢 **Cura inmunológica** | **Erradicación de memoria alérgica** | **Aquí trabajamos** |

La cura definitiva requiere la eliminación o reprogramación de las **células plasmáticas de vida larga (LLPC) IgE+** — células quiescentes que residen en nichos protegidos del bazo y la médula ósea, y que son responsables de la persistencia de la IgE incluso sin exposición al alérgeno.

---

## 🏗️ Arquitectura del pipeline

```
┌─────────────────────────────────────────────────────────────────┐
│                       AllerCure-LATAM                           │
│                                                                  │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐  │
│  │  FASE I  │───▶│  FASE II │───▶│ FASE III │───▶│ FASE IV  │  │
│  │ Epítopos │    │ Diseño   │    │ Ensayo   │    │    ML    │  │
│  │ & MHC II │    │ sgRNA /  │    │ Clínico  │    │ Predicción│  │
│  │ (IEDB)   │    │ ARNm-LNP │    │ Virtual  │    │ AUC 0.896│  │
│  └──────────┘    └──────────┘    └──────────┘    └──────────┘  │
│        │               │               │               │        │
│        ▼               ▼               ▼               ▼        │
│  BioPython        CRISPR-Cas9     Biomarcadores    XGBoost +    │
│  MHC binding      target pred.    Δ₁₅w tracking   SHAP explain │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📐 Fases del proyecto

### Fase I — Mapeo de epítopos y predicción de afinidad MHC II

Identificación computacional de los péptidos de **Der p 1**, **Der p 2**, **Der p 23** y **Der p 7** con mayor afinidad por alelos MHC II prevalentes en poblaciones latinoamericanas (HLA-DR, HLA-DQ).

- **API principal:** [IEDB Analysis Resource](https://www.iedb.org/)
- **Herramientas:** `BioPython`, `mhcflurry`, `NetMHCIIpan`
- **Salida:** Tabla priorizada de péptidos candidatos para vacuna de epítopos

**Péptidos de alta prioridad identificados en literatura:**
```
Der p 23 → 'CPSRFGYFADPKDPH'  (alta afinidad pan-alélica)
Der p 23 → 'CPGNTRWNEDEETCT'  (conservado en cepas LATAM)
```

---

### Fase II — Diseño de intervención génica y plataformas de entrega

Diseño *in silico* de:
1. **sgRNA para CRISPR-Cas9** dirigidos a la región Sε del locus IgE y genes pro-Th2 (IL-4, IL-13, GATA-3)
2. **Evaluación de vacunas ARNm-LNP** bivalentes (Der p 1 + Der p 2, ratio 1:1)
3. **Nanopartículas PLGA** conjugadas con agonistas TLR9 (oligonucleótidos CpG)

El foco especial está en la **eliminación de LLPC IgE+** mediante senolíticos inmunológicos (e.g., Navitoclax, inhibidor de BCL2/BCLXL/BCLW).

---

### Fase III — Protocolo de ensayo clínico virtual

Simulación de un ensayo clínico aleatorizado doble ciego siguiendo guías **EAACI** y metodología **GRADE**, con criterios de valoración por fase:

```
V0  (Tamizaje)      → sIgE, Der p 23 sIgE, FeNO
S15 (Inducción)     → ΔsIgE, ΔsIgG4, activación de basófilos
M36 (Mantenimiento) → Treg FoxP3+, IgA mucosal, CSMS
M60 (Post-trat.)    → Provocación nasal/bronquial, IgE de vida larga
```

---

### Fase IV — Machine Learning para predicción de eficacia

Modelo de regresión logística binaria con **AUC = 0.896**, entrenado sobre respuesta temprana a semana 15:

```
y = −6.749 + 1.823·(V1 CSMS) + 0.068·(Δ₁₅w Der f1 sIgE) + 0.003·(Δ₁₅w Der p23 sIgG4)
```

Interpretabilidad con **SHAP** para identificar por qué el algoritmo clasifica a un paciente como "no respondedor" (ej. baja inducción de IgG4 contra Der p 23).

**Algoritmos implementados:**
- `XGBoost` — predicción principal
- Random Survival Forests (RSF) — análisis de supervivencia
- Latent Class Analysis (LCA) — fenotipado de subgrupos
- `SHAP` — explicabilidad clínica

---

## ⚙️ Instalación

### Opción A — Google Colab (recomendado para el hackathon)

```python
%%capture
!pip install biopython xgboost shap mhcflurry scikit-survival imbalanced-learn
!mhcflurry-downloads fetch
```

### Opción B — Entorno local

```bash
git clone https://github.com/tu-org/allercure-latam.git
cd allercure-latam
python -m venv venv
source venv/bin/activate        # Linux/Mac
# venv\Scripts\activate         # Windows
pip install -r requirements.txt
```

**Requisitos del sistema:**
- Python 3.10+
- RAM ≥ 8 GB (16 GB recomendado para predicción MHC)
- GPU opcional (acelera entrenamiento de modelos ML)

---

## 🚀 Uso rápido

```python
from allercure import EpitopePipeline, MLPredictor

# Fase I: Mapeo de epítopos
pipeline = EpitopePipeline(allergens=["Der_p_1", "Der_p_23"])
epitopes = pipeline.predict_mhc2_binding(
    population="LATAM",          # alelos HLA frecuentes en LATAM
    threshold_percentile=10      # top 10% de afinidad
)
epitopes.to_csv("outputs/epitopes_prioritized.csv")

# Fase IV: Predicción de respuesta
predictor = MLPredictor.load("models/xgb_week15_v2.pkl")
patient = {
    "V1_CSMS": 2.4,
    "delta_Der_f1_sIgE": -0.38,
    "delta_Der_p23_sIgG4": 120.5
}
result = predictor.predict(patient)
print(f"Probabilidad de respuesta: {result['probability']:.1%}")
print(f"Factores clave (SHAP): {result['top_features']}")
```

---

## 📁 Estructura del repositorio

```
allercure-latam/
│
├── 📓 notebooks/
│   ├── 01_epitope_mapping.ipynb        # Fase I: IEDB + MHC binding
│   ├── 02_sgrna_design.ipynb           # Fase II: CRISPR target design
│   ├── 03_clinical_trial_sim.ipynb     # Fase III: simulación ensayo
│   └── 04_ml_efficacy_predictor.ipynb  # Fase IV: XGBoost + SHAP
│
├── 🐍 allercure/
│   ├── __init__.py
│   ├── epitopes.py          # Predicción MHC II y mapeo
│   ├── crispr.py            # Diseño sgRNA y off-target analysis
│   ├── biomarkers.py        # Tracking Δ₁₅w y biomarcadores
│   ├── ml_predictor.py      # Modelos ML e interpretabilidad
│   └── latam_hla.py         # Base de alelos HLA frecuentes en LATAM
│
├── 📊 data/
│   ├── raw/                 # Secuencias alérgenos Der p 1/2/7/23
│   ├── hla_latam/           # Frecuencias alélicas por país
│   └── clinical/            # Datos clínicos anonimizados
│
├── 🤖 models/
│   ├── xgb_week15_v2.pkl
│   └── rsf_survival_v1.pkl
│
├── 📈 outputs/              # Resultados generados
├── 🧪 tests/                # Tests unitarios e integración
├── requirements.txt
├── environment.yml          # Conda environment
└── README.md
```

---

## 📊 Biomarcadores clave

| Biomarcador | Fase | Significado clínico | Valor umbral |
|---|---|---|---|
| `sIgE Der p 23` | Tamizaje | Sensibilización infradiagnosticada | > 0.35 kUA/L |
| `ΔsIgG4 semana 15` | Inducción | Respuesta bloqueadora temprana | ↑ > 2× basal |
| `Treg FoxP3+` | Mantenimiento | Tolerancia activa establecida | > 15% de CD4+ |
| `FeNO` | Tamizaje | Inflamación eosinofílica | > 25 ppb |
| `Δ₁₅w` | Predicción | Biomarcador compuesto del modelo ML | Score > 0.5 |

---

## 🤖 Modelo predictivo

El modelo de semana 15 permite **interrumpir protocolos ineficaces de forma temprana**, evitando años de tratamiento innecesario y permitiendo personalizar dosis según el fenotipo del paciente.

```
Performance del modelo:
  AUC-ROC:    0.896
  Sensibilidad: 84.2%
  Especificidad: 78.9%
  Variables:  3 (CSMS + 2 biomarcadores séricos)
```

**Implementación de SHAP para explicabilidad clínica:**
```python
import shap

explainer = shap.TreeExplainer(model)
shap_values = explainer.shap_values(X_patient)
shap.waterfall_plot(shap_values[0])
# → Muestra al clínico qué variables condujeron la predicción
```

---

## 🌎 Consideraciones LATAM

Este proyecto incorpora factores epidemiológicos y genómicos **específicos de América Latina** que los modelos entrenados en cohortes europeas o asiáticas suelen ignorar:

- **Alelos HLA prevalentes:** HLA-DRB1\*04:07, HLA-DRB1\*08:02, HLA-DRB1\*14:06 — frecuentes en mestizos latinoamericanos, con perfiles de presentación antigénica distintos.
- **Clima tropical/subtropical:** Mayor carga de alérgenos de ácaros durante todo el año (sin estacionalidad), lo que implica sensibilización más temprana y grave.
- **Der p 23 infradiagnosticado:** Prevalencia de sensibilización hasta 74% en asmáticos, pero ausente en muchos extractos comerciales disponibles en la región.
- **Acceso desigual a AIT:** El pipeline prioriza soluciones adaptables a contextos de recursos limitados (plataforma Colab, APIs gratuitas, modelos ligeros).
- **Diversidad genómica:** Integración de datos del Proyecto Genoma LATAM para mejorar la predicción de respuesta por fenotipo.

---

## 🗺️ Roadmap

```
v0.1 (Hackathon)  ✅  Pipeline base Fases I y IV en Colab
v0.2              🔲  Integración IEDB API + HLA LATAM database
v0.3              🔲  Módulo CRISPR off-target analysis
v0.4              🔲  Dashboard clínico interactivo (Streamlit)
v0.5              🔲  Validación con datos clínicos colombianos/chilenos
v1.0              🔲  Paper + dataset público anonimizado
```

---

## 🤝 Contribuir

¡Las contribuciones son bienvenidas, especialmente desde equipos latinoamericanos!

```bash
# 1. Fork del repositorio
# 2. Crea tu rama
git checkout -b feature/nueva-funcionalidad

# 3. Haz tus cambios y commitea
git commit -m "feat: agrega soporte para alelos HLA de población andina"

# 4. Push y Pull Request
git push origin feature/nueva-funcionalidad
```

**Áreas prioritarias:**
- Datos clínicos anonimizados de pacientes LATAM
- Frecuencias alélicas HLA por país/región
- Validación del modelo en cohortes locales
- Traducción de notebooks al español y portugués

Ver [`CONTRIBUTING.md`](./CONTRIBUTING.md) para guías detalladas.

---

## 📄 Citación

Si usas este pipeline en tu investigación:

```bibtex
@software{allercure_latam_2025,
  title     = {AllerCure-LATAM: Pipeline computacional para la cura de alergias a ácaros en Latinoamérica},
  author    = {Equipo AllerCure-LATAM},
  year      = {2025},
  url       = {https://github.com/tu-org/allercure-latam},
  note      = {Desarrollado en el marco de Hackathon LATAM Biotech}
}
```

**Referencias científicas clave:**
- IEDB Analysis Resource — [iedb.org](https://www.iedb.org)
- Der p 23 como alérgeno mayor — Thermo Fisher / Phadia d209
- Modelo predictivo semana 15 — PMC12416371
- LLPC IgE+ y BCL2 — PMC9760851
- Vacunas ARNm-LNP Der p 1/2 — PubMed 41485046

---

## 👥 Equipo

Desarrollado con ❤️ desde Latinoamérica para Latinoamérica.

> *"La inversión en estas tecnologías no solo mejorará la calidad de vida de millones de personas, sino que también transformará el sistema de salud, desplazando el gasto desde el manejo crónico de síntomas hacia intervenciones curativas definitivas."*

---

<p align="center">
  <strong>AllerCure-LATAM</strong> · MIT License · 2025
</p>
