# 🌿 Jamestown ACV — Applied Computer Vision
## Global Solution FIAP 2026 — Engenharia de Software

---

## 📋 Sobre o Projeto

O módulo de **Applied Computer Vision** do Jamestown Autonomous Hub é responsável pela triagem visual autônoma de amostras foliares coletadas.

A CNN analisa a imagem da folha e classifica sua condição, gerando um diagnóstico em JSON que alimenta o pipeline de Big Data (BDDI), o modelo preditivo (GAIE) e o bot de contingência (RPA).

---

## 🎯 Classes de Diagnóstico

| Classe | Label | Descrição |
|--------|-------|-----------|
| 0 | `estresse` | Desidratação, deficiência mineral ou desequilíbrio de cultivo |
| 1 | `fungo_patogeno` | Necrose, manchas fúngicas ou risco microbiológico |
| 2 | `saudavel` | Folha com coloração e estrutura compatíveis com cultivo estável |

---

## 📁 Dataset

- **Fonte:** PlantVillage (adaptado para contexto espacial)
- **Total:** 9.000 imagens balanceadas (3.000 por classe)
- **Divisão:** 70% treino / 15% validação / 15% teste
- **Pré-processamento:** redimensionamento 128x128, normalização 0-1
- **Augmentation:** rotação ±15°, zoom 10%, flip horizontal, variação de brilho

---

## 🧠 Arquiteturas CNN

### CNN A — Leve (modelo selecionado)
- 3 blocos Conv2D + ReLU + MaxPooling
- Flatten → Dense(128) → Dropout(0.4) → Softmax
- **3.305.027 parâmetros**
- **Acurácia no teste: 92,37%** ✅

### CNN B — Profunda
- 4 blocos Conv2D + BatchNormalization + MaxPooling
- Dense(256) → Dropout(0.5) → Dense(128) → Dropout(0.3) → Softmax
- **4.618.179 parâmetros**
- Apresentou overfitting — validação instável (~86%)

### Por que a CNN A foi escolhida?
A CNN B, apesar de mais profunda, apresentou instabilidade na validação com loss chegando a 7.3 em alguns epochs. A CNN A demonstrou curvas mais estáveis e acurácia de validação superior (93,6%), sendo mais adequada para operação em ambiente de missão crítica.

---

## 📊 Métricas — CNN A (conjunto de teste)

| Classe | Precision | Recall | F1-Score |
|--------|-----------|--------|----------|
| estresse | 0.90 | 0.91 | 0.91 |
| fungo_patogeno | 0.92 | 0.87 | 0.89 |
| saudavel | 0.95 | 0.99 | 0.97 |
| **média** | **0.92** | **0.92** | **0.92** |

> **Destaque operacional:** fungo classificado como saudável ocorreu apenas 1 vez em 450 testes — risco crítico minimizado.

---

## 🔗 Saída JSON (integração com o ecossistema)

```json
{
  "sample_id": "JAM-GH01-SMP-0042",
  "greenhouse_id": "GH-01",
  "sector": "GH-01",
  "predicted_class": 1,
  "predicted_label": "fungo_patogeno",
  "leaf_status": "fungo_patogeno",
  "confidence": 0.9957,
  "model_version": "cnn_a_v1",
  "timestamp": "2026-05-27T03:19:05Z",
  "recommended_action": "ISOLAR SETOR — Acionar protocolo antifúngico"
}
```

---

## 🚀 Como executar

### Opção 1 — Google Colab (recomendado)
1. Abra o notebook `jamestown_acv.ipynb` no Google Colab
2. Ative GPU: **Ambiente de execução → T4 GPU**
3. Execute todas as células em ordem
4. A interface Gradio abrirá automaticamente com link público

### Opção 2 — Local
```bash
pip install tensorflow gradio scikit-learn matplotlib
jupyter notebook jamestown_acv.ipynb
```

---

## 📂 Estrutura do repositório
acv/
├── notebooks/
│   └── jamestown_acv.ipynb
├── models/
│   ├── cnn_a_jamestown.h5
│   └── cnn_b_jamestown.keras
├── evidencias/
│   ├── CNN_A_Leve_historico.png
│   ├── CNN_B_Profunda_historico.png
│   └── CNN_A_matriz_confusao.png
├── data/
│   └── jamestown_sample_output.json
└── README.md

---

## ⚠️ Modelos Pré-treinados

Os arquivos de modelo (`cnn_a_jamestown.h5` e `cnn_b_jamestown.keras`) 
ultrapassam o limite de 100MB do GitHub e não estão incluídos no repositório.

**Para reproduzir os modelos**, execute o notebook `jamestown_acv.ipynb` 
no Google Colab com GPU ativada. O treinamento completo leva aproximadamente 
20 minutos e os modelos serão salvos automaticamente.

---

## 🔄 Integração no Ecossistema Jamestown
PBML (braço coleta amostra)
↓
ACV (CNN classifica folha)  
↓
BDDI (Airflow ingere o JSON)
↓
GAIE (modelo prevê risco + SHAP)
↓
BISD (dashboard exibe alerta)
↓
RPA (abre incidente + envia e-mail)
↓
SDTCC (Azure monitora tudo)

---

## 👥 Equipe

**Jamestown Autonomous Hub — Global Solution FIAP 2026**  
Engenharia de Software — 4º Ano
Rafael Franck RM550875
Gabriela Trevisan RM99500
Breno Silva RM99275
Eduardo Araujo RM99758
Gustavo Akio RM550241
---

## 📎 ODS Relacionados

- **ODS 2** — Fome zero e agricultura sustentável
- **ODS 9** — Indústria, inovação e infraestrutura
