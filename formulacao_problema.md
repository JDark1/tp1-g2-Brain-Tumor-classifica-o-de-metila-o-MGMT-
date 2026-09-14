# Formulação e solução do problema — G3
### RSNA 2021 — Brain Tumor (classificação de metilação MGMT)

## Formulação do problema

Dado um exame de ressonância magnética multi-modal (FLAIR, T1w, T1wCE, T2w)
de um paciente com glioma, o objetivo é classificar, a partir de
características extraídas das imagens, se o status de metilação do promotor
MGMT do tumor é positivo ou negativo. A unidade de amostra é o paciente
(agregando informação de todos os cortes/modalidades disponíveis), e a saída
é uma classificação binária (0 = não metilado, 1 = metilado).

## Contexto clínico

A metilação do promotor MGMT é o principal fator preditivo de resposta à
temozolomida em glioblastoma: no ensaio clínico randomizado de referência da
área, apenas pacientes com promotor metilado obtiveram ganho de sobrevida
com o tratamento (Hegi et al., 2005). Por isso, determinar esse status
influencia diretamente a decisão terapêutica. Hoje essa determinação é
invasiva (biópsia + análise molecular), o que motiva o interesse em prevê-la
de forma não invasiva a partir de imagem (radiogenômica) — o problema deste
TP1.

## Por que este é um problema de sinal fraco (embasamento na literatura)

Diferente do que se poderia supor, a dificuldade deste desafio não está
(apenas) na engenharia de features, mas em limitações estruturais do
próprio problema, documentadas na literatura:

1. **O próprio rótulo carrega incerteza.** Não há consenso sobre qual
   técnica de medição (pirosequenciamento, MSP, imuno-histoquímica) nem
   sobre quais sítios CpG ou ponto de corte usar para definir "metilado"
   (Brandner et al., 2021; Gibson et al., 2024). Diferentes instituições que
   compuseram o dataset BraTS podem ter usado protocolos distintos.
2. **Reduzir a metilação a um binário descarta informação.** A extensão da
   metilação (tratada como variável contínua) tem valor prognóstico que se
   perde ao dicotomizar em metilado/não metilado (Poon et al., 2021) — uma
   limitação herdada do desenho do próprio dataset, não do pipeline do G3.
3. **Validação externa no próprio desafio do G3 mostrou desempenho próximo
   do acaso.** Kim et al. (2022) validaram externamente, em larga escala
   (420 experimentos, 2 centros), os modelos submetidos ao desafio
   BraTS 2021 de radiogenômica — o mesmo desafio deste TP1 — e concluíram
   que a maioria não se distinguiu do acaso, mesmo usando deep learning
   (proibido neste TP1, mas usado nesses modelos de referência).
4. **O padrão da área é desempenho inflado por validação inadequada.**
   Doniselli et al. (2024), em revisão sistemática com as escalas RQS e
   TRIPOD, mostraram que estudos de radiômica para MGMT com validação
   externa têm desempenho sistematicamente menor que estudos sem validação
   externa — reforçando a necessidade de protocolo experimental rigoroso.

**Consequência prática:** o baseline do G3 deve ser avaliado com expectativa
realista (AUC/acurácia próximos de 0.6–0.75 já são consistentes com a
literatura), e o rigor do protocolo de validação (partição por paciente,
CV com desvio-padrão reportado) importa tanto quanto o desempenho bruto —
justamente porque é onde a maioria dos estudos da área falha.

## Decisões metodológicas tomadas

- **Amostragem:** estratificada por classe, 30 pacientes por classe, semente
  fixa = 42 (documentado em `outputs/eda/amostra_g3.csv`).
- **Validação:** partição por paciente (nunca por corte), com validação
  cruzada agrupada e relato de média ± desvio-padrão entre dobras —
  diretamente embasado em Doniselli et al. (2024) e no alerta de Kim et al.
  (2022) sobre generalização.
- **Rótulo binário aceito como dado do desafio, com ressalva discutida.**
  Poon et al. (2021) mostra que a informação de grau se perde, mas o
  dataset do desafio só fornece o rótulo binário — a limitação será
  registrada explicitamente na seção de Limitações do artigo.
- **Tratamento da diferença de planos entre modalidades:** confirmado
  empiricamente na EDA do Marco 1
  (`outputs/eda/orientacao_por_modalidade_exemplo.csv`) — as 4 modalidades
  do mesmo paciente aparecem em planos de aquisição diferentes. Decisão
  pendente para o Marco 2: registrar/reamostrar as modalidades entre si, ou
  extrair features por modalidade separadamente e combinar apenas no nível
  de vetor de features.
- **Baseline trivial obrigatório:** classificador de classe majoritária,
  como referência mínima antes de qualquer modelo (exigência do TP1, §4.3).

## Observações da EDA (Marco 1)

- Distribuição de classes: 278 (47,5%) não metilado vs. 307 (52,5%)
  metilado — aproximadamente balanceado. A dificuldade dominante deste
  desafio não é desbalanceamento de classes, e sim a fraqueza intrínseca do
  sinal (ver seção acima) — achado consistente com Kim et al. (2022) e
  Doniselli et al. (2024).
- 4 modalidades do mesmo paciente confirmadas em planos de aquisição
  diferentes (coronal, axial, sagital) via `ImageOrientationPatient`.
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
- [ ] Primeira família de descritores extraída + um classificador treinado
- [ ] Redigir, na seção de Limitações do artigo final, a ressalva sobre
      incerteza do rótulo (Brandner et al., 2021; Poon et al., 2021) e sobre
      o teto de desempenho documentado por validação externa (Kim et al.,
      2022; Doniselli et al., 2024)

## Referências citadas

1. HEGI, M. E. et al. MGMT gene silencing and benefit from temozolomide in
   glioblastoma. *N Engl J Med*, 352(10):997-1003, 2005.
2. LOUIS, D. N. et al. The 2021 WHO Classification of Tumors of the Central
   Nervous System: a summary. *Neuro-Oncology*, 23(8):1231-1251, 2021.
3. BRANDNER, S. et al. MGMT promoter methylation testing to predict overall
   survival in people with glioblastoma treated with temozolomide: a
   comprehensive meta-analysis based on a Cochrane Systematic Review.
   *Neuro-Oncology*, 23(9):1457-1469, 2021.
4. GIBSON, D. et al. A systematic review of high impact CpG sites and
   regions for MGMT methylation in glioblastoma. *BMC Neurology*,
   24(1):103, 2024.
5. TESKE, N. et al. Extent, pattern, and prognostic value of MGMT promotor
   methylation: does it differ between glioblastoma and
   IDH-wildtype/TERT-mutated astrocytoma? *J Neuro-Oncol*, 156(2):317-327,
   2022.
6. POON, M. T. C. et al. Extent of MGMT promoter methylation modifies the
   effect of temozolomide on overall survival in patients with
   glioblastoma: a regional cohort study. *Neuro-Oncology Advances*,
   3(1):vdab171, 2021.
7. CHOI, H. J. et al. MGMT promoter methylation status in initial and
   recurrent glioblastoma: correlation study with DWI and DSC PWI features.
   *AJNR*, 42(5):853-860, 2021.
8. YOGANANDA, C. G. B. et al. MRI-based deep-learning method for
   determining glioma MGMT promoter methylation status. *AJNR*,
   42(5):845-852, 2021.
9. KIM, B.-H. et al. Validation of MRI-based models to predict MGMT
   promoter methylation in gliomas: BraTS 2021 radiogenomics challenge.
   *Cancers*, 14(19):4827, 2022.
10. DONISELLI, F. M. et al. Quality assessment of the MRI-radiomics studies
    for MGMT promoter methylation prediction in glioma: a systematic review
    and meta-analysis. *European Radiology*, 34(9):5802-5815, 2024.
