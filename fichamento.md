# Fichamento — G3 (RSNA 2021 Brain Tumor MGMT Methylation)

Baseado na lista padronizada de 10 referências do grupo
(`Referencias_MGMT_padronizado.docx`), organizada em 3 eixos temáticos:
1) Fundamento clínico, 2) Métodos/CpG (como a metilação é medida), 3) Imagem
(prever o marcador por MRI).

---

## Eixo 1 — Fundamento clínico

### 1. Hegi et al. (2005) — *N Engl J Med*
Ensaio clínico randomizado que mostrou que apenas pacientes com promotor
MGMT metilado obtiveram ganho de sobrevida com temozolomida. É o estudo que
justifica, clinicamente, por que prever o status de MGMT importa — sem ele,
o problema do TP1 seria apenas um exercício técnico sem motivação clínica.
**Relação com o TP1:** vai na Introdução, como justificativa do porquê a
predição de MGMT é clinicamente relevante (contexto clínico exigido em
§6.2 do TP1).

### 2. Louis et al. (2021) — *Neuro-Oncology* (Classificação OMS 2021)
Resumo da 5ª edição da classificação da OMS para tumores do SNC, que
consolidou marcadores moleculares (IDH, TERT etc.) como critério
diagnóstico. Referência de nomenclatura para os demais artigos.
**Relação com o TP1:** usar como referência de terminologia/definições ao
descrever o tipo de tumor do desafio (Brain Tumor MGMT é baseado em
glioblastoma/glioma de alto grau).

---

## Eixo 2 — Métodos de medição / sítios CpG

### 3. Brandner et al. (2021) — *Neuro-Oncology* (meta-análise Cochrane)
Compara técnicas de medição de metilação (pirosequenciamento, MSP,
imuno-histoquímica) quanto ao poder prognóstico. Conclui que
pirosequenciamento e MSP superam IHC, e **não há consenso sobre pontos de
corte nem sobre quais sítios CpG usar**.
**Relação com o TP1/Marco 1:** achado crítico para a seção de Limitações —
o próprio rótulo (`MGMT_value`) usado como "verdade" no dataset carrega
incerteza metodológica de origem: diferentes instituições que compuseram o
BraTS podem ter usado protocolos e cortes distintos para gerar esse rótulo
binário. Isso ajuda a explicar por que a predição por imagem tem teto de
desempenho baixo — parte do "ruído" pode estar no próprio rótulo, não só na
imagem.

### 4. Gibson et al. (2024) — *BMC Neurology* (revisão sistemática)
Reúne quais sítios CpG específicos do promotor têm associação significativa
com sobrevida e expressão de MGMT. Complementa Brandner et al. na lacuna de
quais sítios importam.
**Relação com o TP1:** reforça, junto com Brandner, a discussão sobre
incerteza na variável-alvo — útil na Metodologia ou Limitações, não motiva
decisão técnica direta de pré-processamento de imagem.

### 5. Teske et al. (2022) — *J Neuro-Oncol* (coorte retrospectiva)
Compara extensão e padrão de sítios CpG metilados entre glioblastoma e
astrocitoma IDH-selvagem/TERT-mutado. Mais sítios metilados associou-se a
desfecho mais favorável em ambos os grupos.
**Relação com o TP1:** o dataset RSNA 2021 (BraTS) inclui população de
glioma de alto grau que pode abranger subtipos moleculares distintos —
esse artigo é base para discutir, na seção de Limitações, que o grupo não
controla por subtipo molecular (IDH, TERT) ao tratar MGMT isoladamente.

### 6. Poon et al. (2021) — *Neuro-Oncology Advances* (coorte regional, n=414)
Trata a metilação como variável **quantitativa** (grau de metilação) em vez
de binária, e mostra que o ponto de corte binário pode esconder informação
prognóstica relevante.
**Relação com o TP1/Marco 1:** motiva uma ressalva importante na Metodologia
— o dataset do desafio fornece `MGMT_value` como rótulo binário (0/1), o
que já descarta informação de grau que a literatura mostra ser relevante.
Vale citar isso como limitação do desenho do problema herdada do próprio
dataset, não do pipeline do G3.

---

## Eixo 3 — Predição por imagem (radiogenômica)

### 7. Choi et al. (2021) — *AJNR* (correlação DWI/DSC-PWI)
Correlaciona mudanças no status de metilação (tumor inicial vs. recorrente)
com parâmetros de difusão (DWI) e perfusão (DSC-PWI) por MRI, sem uso de
aprendizado de máquina.
**Relação com o TP1:** exemplo de abordagem por imagem convencional
(qualitativa/estatística simples), útil para contraste nos Trabalhos
Relacionados — mostra que a relação MGMT↔imagem já era estudada antes da
radiômica/ML entrarem em cena.

### 8. Yogananda et al. (2021) — *AJNR* (deep learning, T2 apenas)
Rede neural 3D treinada só com T2 para classificar status de metilação,
historicamente citada com acurácia alta (~0.93–0.95 em validação cruzada
interna).
**Relação com o TP1:** não aplicável diretamente como método (o TP1 proíbe
deep learning), mas é o contraponto histórico que o artigo seguinte (Kim et
al., 2022) desmonta com validação externa — bom par para ilustrar o
problema de generalização na seção de Trabalhos Relacionados.

### 9. Kim et al. (2022) — *Cancers* (BraTS 2021 radiogenomics challenge)
**Artigo mais diretamente relevante ao G3**: validação externa em larga
escala (420 experimentos, 2 centros) dos modelos submetidos ao desafio
BraTS 2021 de radiogenômica — **exatamente o desafio que o G3 está
resolvendo**. Conclusão: a maioria dos modelos não se distinguiu do acaso;
os autores concluem que o status de MGMT **pode não ser previsível** a
partir de MRI pré-operatória, mesmo com deep learning.
**Relação com o TP1/Marco 1:** referência central para calibrar expectativa
de desempenho do baseline do G3 — se os modelos vencedores do desafio
oficial (com deep learning, sem a restrição do TP1) não superaram o acaso
em validação externa, um baseline clássico não deve ser julgado
severamente por AUC baixo. Deve entrar tanto na Introdução (justificando o
propósito do baseline) quanto na Conclusão (ao discutir limitações e
resultado esperado).

### 10. Doniselli et al. (2024) — *European Radiology* (revisão + meta-análise)
Avalia qualidade metodológica de estudos de radiômica para MGMT usando as
escalas RQS e TRIPOD. Aderência geralmente baixa às diretrizes; estudos
**com validação externa tiveram desempenho significativamente menor** que
os sem validação externa.
**Relação com o TP1/Marco 1:** justifica diretamente a exigência do TP1 de
protocolo experimental rigoroso (partição por paciente, validação cruzada,
sem vazamento) — o padrão da área é inflar desempenho por má validação, e
esse artigo documenta isso sistematicamente. Serve de embasamento direto
para a seção de Metodologia do G3 ao explicar por que o grupo está sendo
cuidadoso com partição por paciente e ausência de vazamento.

---

## Síntese — como os 10 artigos se conectam ao Marco 1 e ao TP1

1. **Fundamento (1–2):** justificam clinicamente por que prever MGMT
   importa e fixam a terminologia — vão na Introdução.
2. **Medição do rótulo (3–6):** mostram que o próprio "gabarito" (MGMT
   metilado/não metilado) carrega incerteza — diferentes técnicas, sem
   consenso de corte, e que reduzir a um binário descarta informação
   prognóstica (Poon). Isso é uma explicação adicional, além da fraqueza do
   sinal de imagem, para o teto de desempenho observado na área — útil na
   seção de Limitações do artigo do G3.
3. **Imagem (7–10):** traçam a evolução de correlação simples (Choi) →
   deep learning com acurácia aparentemente alta (Yogananda) → desmonte
   dessa acurácia em validação externa no próprio desafio do G3 (Kim et
   al., 2022) → confirmação sistemática de que validação externa reduz
   desempenho em toda a área (Doniselli et al., 2024).
4. **Achado mais acionável para o Marco 2:** o artigo de Kim et al. (2022)
   usa o mesmo desafio (BraTS 2021 radiogenomics) e mostra desempenho
   próximo do acaso em validação externa — isso deve calibrar a meta de
   desempenho do baseline do G3 e reforça, junto com Doniselli et al.
   (2024), a importância do protocolo experimental rigoroso já exigido
   pelo TP1 (§4.4).
