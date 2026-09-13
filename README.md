# TP1 — G3 — RSNA 2021 Brain Tumor (MGMT Methylation)

**Disciplina:** Tópicos Especiais em Sistemas de Informação
**Professor:** Prof. Me. Décio Gonçalves de Aguiar Neto
**Grupo G3:** Alex Oliveira Silva, Kaike Ferreira Alves, João Pedro Pereira Rabelo
**Entrega final:** 04/10/2026

## Objetivo

Construir, avaliar e documentar uma solução **baseline clássica** (sem redes
neurais profundas) para o desafio RSNA-MICCAI Brain Tumor Radiogenomic
Classification: prever, a partir de exames de ressonância magnética, se o
status de metilação do promotor MGMT do tumor é positivo ou negativo.

Restrição do trabalho: proibido uso de deep learning, tanto treinado de ponta
a ponta quanto como extrator de características pré-treinado.

## Dados

Dataset hospedado no Kaggle, via a página do desafio RSNA-MICCAI Brain Tumor
Radiogenomic Classification (RSNA AI Challenges,
https://www.rsna.org/artificial-intelligence/ai-image-challenge).

Requer cadastro e aceite dos termos de uso na página da competição. Os dados
brutos (imagens DICOM) **não** estão neste repositório — apenas o caminho
para obtê-los.

### Amostra utilizada

Amostragem estratificada por classe, 30 pacientes por classe, semente fixa =
42. Critério e contagem documentados em `notebooks/01_eda.ipynb` (seção 6).
Lista de IDs da amostra em `outputs/eda/amostra_g3.csv`.

## Estrutura do repositório

```
TP1_G3_brain-tumor-mgmt/
├── README.md
├── requirements.txt
├── solucao_problema.md        # formulação do problema, decisões e observações
├── notebooks/
│   └── 01_eda.ipynb           # análise exploratória (Marco 1)
└── outputs/
    └── eda/                   # tabelas e figuras geradas pelo notebook de EDA
        ├── distribuicao_classes.csv
        ├── distribuicao_classes.png
        ├── contagem_cortes_por_paciente.csv
        ├── estatisticas_cortes.csv
        ├── metadados_exemplo.csv
        ├── orientacao_por_modalidade_exemplo.csv
        ├── exemplos_visuais_por_classe.png
        └── amostra_g3.csv
```

## Como rodar

1. Acesse o Kaggle e crie um **Notebook** vinculado à competição
   `rsna-miccai-brain-tumor-radiogenomic-classification` (o dataset já vem montado automaticamente em
   `/kaggle/input/competitions/rsna-miccai-brain-tumor-radiogenomic-classification`).
2. Faça upload do notebook `notebooks/01_eda.ipynb` para esse ambiente
   (ou copie o conteúdo das células).
3. Confirme que a variável `DATA_DIR`, na célula de configuração, aponta para:
   ```python
   DATA_DIR = "/kaggle/input/competitions/rsna-miccai-brain-tumor-radiogenomic-classification"
   ```
4. Rode as células em sequência ("Save & Run All"). As saídas (gráficos e CSVs) são geradas
   automaticamente na pasta `outputs_eda_g3/` e podem ser baixadas em seguida.

## Status

- [x] Marco 1 — EDA, distribuição de classes, metadados DICOM, orientação por
      modalidade, exemplos visuais por classe
- [ ] Marco 2 — pipeline de pré-processamento, partição por paciente,
      baseline trivial, primeiro classificador
- [ ] Marco 3 — extração das demais famílias de descritores, experimentos
      comparativos, ablação
- [ ] Marco 4 — análise de erro, artigo final, teste de reprodutibilidade

## Uso de IA generativa

[Preencher ao final, conforme exigido pelo TP1 — declarar onde e para quê IA
generativa foi usada como apoio.]
