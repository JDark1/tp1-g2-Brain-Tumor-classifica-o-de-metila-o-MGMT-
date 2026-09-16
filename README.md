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
Lista de IDs da amostra em `outputs/eda/amostra_g3.csv`. Partição por
paciente (5 dobras, `StratifiedGroupKFold`) definida e **congelada** no
Marco 2, em `outputs/marco2/particao_pacientes_congelada.csv` — não deve ser
alterada nos marcos seguintes.

## Estrutura do repositório

```
TP1_G3_brain-tumor-mgmt/
├── README.md
├── requirements.txt
├── formulacao_problema.md       # formulação do problema, decisões e observações
├── fichamento.md                 # fichamento dos 10 artigos da revisão bibliográfica
├── introducao_trabalhos_relacionados.md  # rascunho das seções 1 e 2 do artigo
├── notebooks/
│   ├── 01_eda.ipynb              # análise exploratória (Marco 1)
│   └── 02_preprocessing_baseline_G3.ipynb  # pré-processamento, partição,
│                                  # baseline trivial, 1ª família de
│                                  # descritores e 1º classificador (Marco 2)
└── outputs/
    ├── eda/                       # tabelas e figuras geradas no Marco 1
    │   ├── distribuicao_classes.csv
    │   ├── distribuicao_classes.png
    │   ├── contagem_cortes_por_paciente.csv
    │   ├── estatisticas_cortes.csv
    │   ├── metadados_exemplo.csv
    │   ├── orientacao_por_modalidade_exemplo.csv
    │   ├── exemplos_visuais_por_classe.png
    │   └── amostra_g3.csv
    └── marco2/                    # tabelas e figuras geradas no Marco 2
        ├── exemplo_pre_processamento.png
        ├── particao_pacientes_congelada.csv
        ├── baseline_trivial_metricas.csv
        ├── baseline_trivial_resumo.csv
        ├── features_glcm_t1wce_g3.csv
        ├── experimentos_parametros_glcm.csv
        ├── logreg_glcm_metricas.csv
        ├── logreg_glcm_resumo.csv
        └── comparativo_baseline_vs_logreg.csv
```

## Como executar

1. Criar um Notebook novo na página do desafio no Kaggle.
2. Adicionar o dataset da competição em "Add Input".
3. Importar `notebooks/01_eda.ipynb` e, em seguida,
   `notebooks/02_preprocessing_baseline_G3.ipynb` (File > Import Notebook)
   ou copiar as células — o segundo depende da amostra salva pelo primeiro
   (`outputs_eda_g3/amostra_g3.csv`).
4. Rodar todas as células de cada notebook ("Save & Run All" / Commit), para
   gerar e salvar as pastas `outputs_eda_g3/` e `outputs_marco2_g3/` com as
   tabelas e figuras.

Nenhuma dependência além das listadas em `requirements.txt` é necessária. Os
notebooks não usam caminhos absolutos de máquina local — apenas
`/kaggle/input/...` e pastas de saída relativas.

## Principais resultados até o momento (Marco 2)

- Baseline trivial (classe majoritária): acurácia média 0,40 entre as 5
  dobras (efeito esperado de amostra pequena e quase balanceada — ver
  `formulacao_problema.md`).
- Primeira família de descritores (GLCM/Haralick, modalidade T1wCE,
  distância 1, 32 níveis de cinza — configuração escolhida por
  experimentação, ver seção 6b do notebook) + regressão logística: AUC
  média 0,751 ± 0,114, acima do baseline trivial.

## Status

- [x] Marco 1 — EDA, distribuição de classes, metadados DICOM, orientação por
      modalidade, exemplos visuais por classe
- [x] Marco 2 — pipeline de pré-processamento, partição por paciente
      congelada, baseline trivial, primeira família de descritores e
      primeiro classificador, experimentação de parâmetros
- [ ] Marco 3 — extração das demais famílias de descritores (mínimo 3 no
      total), mais modelos clássicos, grade comparativa descritor × modelo,
      estudo de ablação
- [ ] Marco 4 — análise de erro, artigo final, teste de reprodutibilidade

## Uso de IA generativa

O assistente de IA Claude (Anthropic) foi utilizado como apoio nas seguintes
atividades, com verificação e validação do grupo em todos os casos:

- Estruturação e rascunho de código dos notebooks de EDA (Marco 1) e de
  pré-processamento/baseline (Marco 2), incluindo as funções de leitura
  DICOM, o pipeline de pré-processamento, a extração de descritores GLCM e o
  protocolo de validação cruzada por paciente;
- Busca e organização do fichamento dos 10 artigos da revisão bibliográfica
  (`fichamento.md`);
- Rascunho inicial das seções de Introdução e Trabalhos Relacionados do
  artigo (`introducao_trabalhos_relacionados.md`);
- Apoio na interpretação de resultados experimentais (ex.: comparação de
  parâmetros do GLCM e escolha da modalidade T1wCE) e na redação de
  observações metodológicas e limitações.

Todo o conteúdo técnico, as decisões metodológicas e as referências
bibliográficas foram revisados e validados pelo grupo antes da incorporação
ao projeto.
