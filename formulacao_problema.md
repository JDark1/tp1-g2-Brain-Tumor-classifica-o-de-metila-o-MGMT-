# Formulação e solução do problema — G3
### RSNA 2021 — Brain Tumor (classificação de metilação MGMT)

## Formulação do problema

Dado um exame de ressonância magnética multi-modal (FLAIR, T1w, T1wCE, T2w)
de um paciente com glioma, o objetivo é classificar, a partir de
características extraídas das imagens, se o status de metilação do promotor
MGMT do tumor é positivo ou negativo. A unidade de amostra é o paciente
(agregando informação de todos os cortes/modalidades disponíveis), e a saída
é uma classificação binária (0 = não metilado, 1 = metilado).

Trata-se de um problema **radiogenômico**: usar imagem para inferir uma
característica molecular do tumor que hoje só é obtida de forma invasiva
(biópsia + pirosequenciamento ou PCR bissulfito quantitativo — conforme
descrito em Baid et al., 2021). A literatura fichada mostra que esse é um
problema de **sinal fraco**: mesmo estudos com pipelines robustos e centenas
de pacientes reportam acurácia/AUC tipicamente entre 0.6 e 0.75 (Sasaki et
al., 2019; Zheng et al., 2024), raramente ultrapassando 0.85–0.90 mesmo com
seleção de features sofisticada (Do et al., 2022, com ressalva de
possível overfitting dado o n pequeno daquele estudo).

## Decisões metodológicas tomadas

- **Amostragem:** estratificada por classe, 30 pacientes por classe, semente
  fixa = 42 (documentado em `outputs/eda/amostra_g3.csv`).
- **Combinação de modalidades:** extrair descritores das 4 modalidades
  (FLAIR, T1w, T1wCE, T2w) e combiná-las, em vez de usar uma única —
  decisão embasada em dois achados independentes da literatura (Li et al.,
  2024; Zheng et al., 2024), que mostram que o modelo combinando todas as
  sequências supera qualquer sequência isolada.
- **Prioridade da sequência T1 (ou T1 pós-contraste):** três dos cinco
  artigos fichados (Li 2024, Zheng 2024, indiretamente Sasaki 2019) apontam
  T1/T1c como a sequência isolada mais informativa para MGMT — usar isso
  para priorizar esforço de engenharia de features caso o tempo do grupo
  seja limitado.
- **Tratamento da diferença de planos entre modalidades:** confirmado tanto
  empiricamente (EDA do Marco 1 — `outputs/eda/orientacao_por_modalidade_exemplo.csv`)
  quanto pela documentação oficial do dataset (Baid et al., 2021, que
  descreve que o Task 2/Kaggle — usado pelo G3 — devolve as imagens ao
  espaço original do paciente por modalidade, sem manter co-registro entre
  sequências). Decisão pendente para o Marco 2: registrar/reamostrar as
  modalidades entre si antes de extrair features conjuntas, ou extrair
  features por modalidade separadamente e só combinar no nível de vetor de
  features (sem tentar alinhar pixel a pixel).
- **Validação:** partição por paciente (nunca por corte), com validação
  cruzada agrupada e relato de média ± desvio-padrão entre dobras —
  reforçado pelo caso de alerta de Do et al. (2022), cujo desempenho muito
  acima da literatura com n=53 sugere risco de overfitting quando a
  validação não é rigorosa.
- **Baseline trivial obrigatório:** classificador de classe majoritária,
  como referência mínima antes de qualquer modelo (exigência do TP1, §4.3).

## Observações da EDA (Marco 1)

- Distribuição de classes: 278 (47,5%) não metilado vs. 307 (52,5%)
  metilado — aproximadamente balanceado. Diferente de outros desafios RSNA
  (ex.: mamografia, ~2% positivo), a dificuldade dominante deste desafio
  **não é desbalanceamento de classes**, e sim a fraqueza intrínseca do
  sinal genético em relação à imagem estrutural — achado consistente com a
  literatura fichada (ver Sasaki et al., 2019).
- 4 modalidades do mesmo paciente confirmadas em **planos de aquisição
  diferentes** (coronal, axial, sagital) via `ImageOrientationPatient` —
  característica documentada do pipeline Task 2 do BraTS 2021 (Baid et al.,
  2021), não um erro de leitura.
- Grande variação no número de cortes por modalidade entre pacientes — afeta
  diretamente a estratégia de agregação por exame (2D/2.5D/3D), ainda em
  aberto.

## Pendências para o Marco 2

- [ ] Decidir e implementar a estratégia de registro/reamostragem entre
      modalidades (ou optar por não registrar e justificar)
- [ ] Definir a estratégia de agregação de cortes por paciente (2D com
      pooling / 2.5D / 3D)
- [ ] Implementar o pipeline de pré-processamento (janelamento, normalização
      de intensidade, recorte de ROI)
- [ ] Partição por paciente com `StratifiedGroupKFold`, congelada
- [ ] Baseline trivial rodando com métrica reportada
- [ ] Primeira família de descritores extraída (priorizar T1/T1c conforme
      literatura) + um classificador treinado

## Referências citadas

- Sasaki, T. et al. (2019). Radiomics and MGMT promoter methylation for
  prognostication of newly diagnosed glioblastoma. *Scientific Reports*,
  9:14435.
- Li, L. et al. (2024). Preoperative prediction of MGMT promoter methylation
  in glioblastoma based on multiregional and multi-sequence MRI radiomics
  analysis. *Scientific Reports*, 14:16031.
- Do, D. T. et al. (2022). Improving MGMT methylation status prediction of
  glioblastoma through optimizing radiomics features using genetic
  algorithm-based machine learning approach. *Scientific Reports*, 12:13412.
- Baid, U. et al. (2021). The RSNA-ASNR-MICCAI BraTS 2021 Benchmark on Brain
  Tumor Segmentation and Radiogenomic Classification. *arXiv:2107.02314*.
- Zheng, F. et al. (2024). Radiomics for predicting MGMT status in cerebral
  glioblastoma: comparison of different MRI sequences. *Journal of
  Radiation Research*, 65(3):350–359.
