# Fichamento — G3 (RSNA 2021 Brain Tumor MGMT Methylation)

Cinco artigos lidos na íntegra (Marco 1, Semana 1), todos com foco em predição
de metilação MGMT a partir de radiômica/ML clássico — priorizando métodos
compatíveis com a restrição do TP1 (sem deep learning).

---

## 1. Sasaki et al. (2019) — "Radiomics and MGMT promoter methylation for
prognostication of newly diagnosed glioblastoma"
*Scientific Reports 9:14435*

- **Dataset:** 201 pacientes GBM (10 instituições, rede Kansai), preditos com
  MRI pré-operatória (T1, T1Gd, T2). 162 datasets completos para T1/T2/Gd-T1.
- **Método:** VOIs manuais (core e edema) → normalização de intensidade →
  489 features de textura (1ª e 2ª ordem: GLCM, GLRLM) + forma + localização
  (registro no atlas MNI152). Seleção via LASSO e análise de componentes
  principais supervisionada (SPCA).
- **Resultado principal:** radiômica conseguiu estratificar risco de sobrevida
  (p=0.004), mas a **predição direta do status MGMT teve acurácia de apenas
  67%** — comparável a outros estudos (71–73%), indicando que a predição de
  MGMT por radiômica estrutural isolada tem teto de desempenho baixo.
- **Relação com o TP1/Marco 1:** justifica por que MGMT é um alvo "de sinal
  fraco" (conforme já indicado na tabela de dificuldades do TP1, §5) — mesmo
  com pipeline robusto e localização anatômica, a acurácia fica pouco acima
  do acaso em cenário binário balanceado. Serve de piso de expectativa
  realista para o baseline do G3: não esperar acurácia alta só com
  descritores clássicos em uma única modalidade/ROI.

---

## 2. Li et al. (2024) — "Preoperative prediction of MGMT promoter
methylation in glioblastoma based on multiregional and multi-sequence MRI
radiomics analysis"
*Scientific Reports 14:16031*

- **Dataset:** 183 pacientes GBM (100 próprios + 43 TCIA + 40 externos),
  4 sequências (T1, T1c, T2, T2-FLAIR) segmentadas por rede 3D U-Net em
  3 ROIs (edema/não-realce, necrose, realce de contraste).
- **Método:** 1223 features de radiômica por ROI×sequência (12 combinações);
  modelos individuais por ROI e por sequência; modelo combinado (ComRad)
  agregando todos via regressão logística.
- **Resultado principal:** o modelo que combina **todas as sequências e ROIs
  (ComRad)** obteve o melhor desempenho (AUC 0.839 treino-externo / 0.739
  validação-externa), superando qualquer sequência ou região isolada. Entre
  sequências isoladas, **T1 teve o melhor desempenho individual** (AUC 0.836).
  Adicionar variáveis clínicas não melhorou o modelo.
- **Relação com o TP1/Marco 1:** apoia diretamente a decisão de **combinar
  as 4 modalidades** em vez de usar uma só, e sugere tratar cada
  região/modalidade como um extrator de features separado antes de combinar
  — relevante para a estratégia de agregação que o G3 ainda vai decidir
  (Marco 2). O achado de que T1 isolado já performa bem é uma pista útil
  para priorizar essa sequência na extração de descritores.

---

## 3. Do et al. (2022) — "Improving MGMT methylation status prediction of
glioblastoma through optimizing radiomics features using genetic
algorithm-based machine learning approach"
*Scientific Reports 12:13412*

- **Dataset:** 53 pacientes GBM (TCGA-GBM via Bakas et al.), 4 modalidades
  (T1, T1-Gd, T2, T2-FLAIR), 704 features de radiômica pré-extraídas.
- **Método:** seleção de features em duas etapas — XGBoost (filtro de
  importância) seguido de algoritmo genético (GA) como wrapper, testando
  SVM, Random Forest e XGBoost como classificador de aptidão.
- **Resultado principal:** GA-RF (Random Forest dentro do GA) atingiu
  sensibilidade 0.894, especificidade 0.966, acurácia 0.925 em validação
  cruzada 5-fold — bem acima da maioria da literatura citada pelos próprios
  autores (0.56–0.90). Amostra pequena (n=53) é uma limitação reconhecida.
- **Relação com o TP1/Marco 1:** referência direta para a etapa de
  **seleção/redução de características** (§4.3 do TP1 — PCA, RFE, importância
  por permutação). O alto desempenho com n pequeno é um alerta de possível
  overfitting — reforça a necessidade do G3 de usar validação cruzada
  agrupada por paciente e reportar desvio-padrão entre dobras, como exige
  o protocolo experimental do TP1 (§4.4).

---

## 4. Baid et al. (2021) — "The RSNA-ASNR-MICCAI BraTS 2021 Benchmark on
Brain Tumor Segmentation and Radiogenomic Classification"
*arXiv:2107.02314*

- **O que é:** artigo oficial que descreve a construção do dataset que o G3
  está usando (2.040 pacientes, 8.000 exames — Task 1: segmentação; Task 2:
  classificação MGMT, a tarefa do Kaggle usada pelo G3).
- **Achado crítico para o G3:** para a Task 1 (segmentação), todas as
  imagens foram **co-registradas a um atlas anatômico comum (SRI24)** e
  reamostradas para resolução isotrópica 1mm³. Porém, para a **Task 2
  (classificação MGMT — exatamente o dataset do Kaggle usado pelo G3)**, as
  imagens já skull-stripped foram **convertidas de volta para o espaço
  original do paciente (DICOM)**, sequência por sequência, sem manter o
  co-registro entre modalidades.
- **Relação com o TP1/Marco 1 — confirma o achado da EDA:** isso explica
  exatamente por que a checagem de `ImageOrientationPatient` no Marco 1
  mostrou FLAIR/T1w/T1wCE/T2w em **planos diferentes** para o mesmo
  paciente — não é erro de leitura, é uma característica documentada do
  pipeline oficial do dataset Task 2. Também traz os rótulos de qualidade
  esperados (erros comuns de segmentação automática, protocolo de
  determinação do status MGMT via pirosequenciamento ou PCR bissulfito
  quantitativo) — útil para a seção de Trabalhos Relacionados e para
  justificar a necessidade de reamostragem/registro no pré-processamento do
  Marco 2.

---

## 5. Zheng et al. (2024) — "Radiomics for predicting MGMT status in
cerebral glioblastoma: comparison of different MRI sequences"
*Journal of Radiation Research 65(3):350–359*

- **Dataset:** 215 pacientes GBM, 7 sequências (T1, T2, CE, FLAIR,
  DWI_alto-b, DWI_baixo-b, ADC) e 3 máscaras (FLAIR, CE, edema).
- **Método:** ~38.500 features radiômicas extraídas (todas combinações
  sequência×máscara); seleção por variância + f-score + mRMR; classificador
  XGBoost, comparado com combinação de todas as sequências vs. sequência
  única.
- **Resultado principal:** o modelo com **todas as sequências combinadas**
  teve AUC 0.754 (validação), superior a qualquer sequência isolada
  (0.538–0.697). Entre as isoladas, **T1WI teve o melhor desempenho**
  (AUC 0.697); CE teve o pior (AUC 0.538) — mesma tendência observada por
  Li et al. (2024).
- **Relação com o TP1/Marco 1:** segunda confirmação independente (além de
  Li et al. 2024) de que **combinar modalidades supera usar uma única**, e
  de que **T1 costuma ser a sequência isolada mais informativa** para MGMT.
  Reforça a decisão metodológica de extrair descritores de todas as 4
  modalidades disponíveis no dataset do G3, em vez de escolher apenas uma.

---

## Síntese — padrões que atravessam os 5 artigos

1. **MGMT é um alvo de sinal fraco por imagem estrutural isolada** (Sasaki
   2019): acurácia costuma ficar na faixa de 0.6–0.7 mesmo com pipelines
   cuidadosos. O G3 deve reportar isso como expectativa realista no
   baseline, não como falha do método.
2. **Combinar modalidades supera usar uma modalidade só** (Li 2024; Zheng
   2024) — motiva a decisão de extrair features de FLAIR, T1w, T1wCE e T2w
   e combiná-las, não escolher uma "melhor".
3. **T1 (ou T1 pós-contraste) tende a ser a sequência isolada mais
   informativa** para MGMT em três dos cinco estudos (Li 2024, Zheng 2024,
   e indiretamente Sasaki 2019) — pode orientar priorização se o tempo do
   grupo for limitado.
4. **Particionamento e validação corretos são decisivos** (Do 2022 com n=53
   e resultado muito acima da literatura é sinal de alerta de overfitting)
   — reforça a exigência do TP1 de partição por paciente e CV com
   desvio-padrão reportado.
5. **A ausência de co-registro entre modalidades no dataset Task 2 do G3 é
   documentada oficialmente** (Baid et al. 2021) — não é bug do notebook de
   EDA, é uma característica do dataset que precisa ser tratada
   explicitamente no pré-processamento do Marco 2.
