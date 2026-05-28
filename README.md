# Previsão de Consumo de Energia em Sines (CP 7520)

Análise exploratória e previsão horária do consumo de energia elétrica no concelho de **Sines** (código postal **7520**), com base em dados públicos da E-REDES.

Comparação de três modelos:
- **LSTM** (rede neuronal recorrente)
- **GRU** (variante mais leve de RNN)
- **XGBoost** (gradient boosting tabular, com 4 conjuntos de features)

---

## Estrutura do projeto

```
Energia_Sines/
├── 01_EDA_Sines.ipynb        # Análise exploratória + criação do dataset final
├── 02_LSTM.ipynb             # Modelo LSTM
├── 03_GRU.ipynb              # Modelo GRU
├── 04_XGBoost.ipynb          # Modelo XGBoost (4 feature sets)
├── data/                     # Datasets (NÃO commitar — ver .gitignore)
│   ├── codigos_postais/      # .xlsx por código postal (E-REDES)
│   ├── consumo-total-nacional.xlsx
│   ├── energia-injetada-na-rede-de-distribuicao.xlsx
│   ├── energia-produzida-total-nacional.xlsx
│   └── dataset_completo_sines.xlsx   # criado pelo notebook 01
├── results/                  # métricas dos modelos (criadas pelos notebooks 02-04)
├── requirements.txt
├── .gitignore
└── README.md
```

---

## Como correr (passo a passo)

### 1. Pré-requisitos

- **Python 3.10 ou 3.11**
- **VSCode** com a extensão **Jupyter** (ou Jupyter Lab)
- Git instalado

### 2. Clonar / abrir o projeto

```bash
cd C:\Users\sofia\Desktop\UTAD\ECD
# (já tens o projeto na pasta Energia_Sines)
code Energia_Sines
```

### 3. Criar ambiente virtual e instalar dependências

No terminal do VSCode (PowerShell):

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install --upgrade pip
pip install -r requirements.txt
```

> No Linux/Mac substitui a 2ª linha por `source .venv/bin/activate`.

### 4. Colocar os dados em `data/`

Cria a pasta `data/` na raiz e coloca lá os ficheiros da E-REDES:

- Coloca os **.xlsx por código postal** dentro de `data/codigos_postais/`
  *(ou diretamente em `data/` — o notebook deteta os dois casos)*
- `consumo-total-nacional.xlsx` em `data/`
- `energia-injetada-na-rede-de-distribuicao.xlsx` em `data/`
- `energia-produzida-total-nacional.xlsx` em `data/`

### 5. Executar os notebooks **na ordem**

1. **`01_EDA_Sines.ipynb`** → cria `data/dataset_completo_sines.xlsx`
2. **`02_LSTM.ipynb`** → treina o LSTM e grava `results/metrics_lstm.csv`
3. **`03_GRU.ipynb`** → treina o GRU e grava `results/metrics_gru.csv`
4. **`04_XGBoost.ipynb`** → treina 4 variantes XGBoost e grava `results/metrics_xgboost.csv`

No VSCode: abre o notebook → escolhe o kernel `.venv` → **Run All**.

---

## Principais correções vs. versão original

### EDA
- ✅ `pivot_table` sem `fill_value=0` (mantém NaN visíveis para diagnóstico)
- ✅ Interpolação temporal das séries (mais correta do que preencher com 0)
- ✅ Gráficos Sines vs Nacional com **dois eixos Y** (escalas muito diferentes)
- ✅ Perfis sazonais **normalizados** (z-score) para comparar dinâmica
- ✅ **Heatmap de correlação**, histograma com KDE, boxplots por hora/dia/mês
- ✅ Outliers apenas **marcados**, não eliminados em massa
- ✅ Unidades (kWh) sempre indicadas nos eixos

### Modelos (LSTM / GRU / XGBoost)
- ✅ **MAPE robusto** (`safe_mape`) — ignora valores próximos de zero (evita divisão por ~0)
- ✅ **Split cronológico 80/20 data-driven** (em vez de uma data fixa que podia não existir nos teus dados)
- ✅ **Seeds fixos** (reprodutibilidade)
- ✅ Cada modelo grava as suas métricas em `results/metrics_*.csv` → fácil de comparar

> ⚠️ Os ficheiros `.xlsx` dentro de `data/` estão **ignorados** pelo `.gitignore`
> (são pesados e públicos da E-REDES — não faz sentido versioná-los).
> Se quiseres incluir o `dataset_completo_sines.xlsx`, remove a linha correspondente do `.gitignore`.

---

Dados: [E-REDES — Open Data](https://e-redes.opendatasoft.com/).
