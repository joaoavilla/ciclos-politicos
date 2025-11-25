# Análise de Ciclos Político-Orçamentários Estaduais (2015–2024)

## 🎯 Objetivo do Projeto

Este projeto tem como objetivo principal realizar uma **análise econométrica de ciclos político-orçamentários de natureza oportunista** nos governos estaduais brasileiros [1]. O foco é verificar empiricamente se os governadores elevam ou realocam despesas em funções orçamentárias mais visíveis ao eleitorado nos períodos que antecedem e coincidem com as eleições estaduais (2018 e 2022) [1, 2].

## 🔎 Metodologia e Dados

### Fontes de Dados

Os dados fiscais foram coletados através da API do **SICONFI** (Sistema de Informações Contábeis e Fiscais do Setor Público Brasileiro) [3, 4].
*   **Cobertura Fiscal:** Demonstrativos das Contas Anuais (DCA), especificamente os anexos I-E (Despesas Empenhadas e Liquidadas por Função), I-D (Despesas Totais) e I-C (Receita do Estado) [5, 6].
*   **Janela Temporal:** Painel UF–Ano cobrindo o período de 2015 a 2024 [1, 7].
*   **Deflator:** O Índice de Preços ao Consumidor Amplo (IPCA) do IBGE (API SIDRA) foi utilizado para deflacionar todas as séries monetárias, ajustando-as para **preços de 2024** [8-10].

### Variáveis e Normalização

As variáveis dependentes são as despesas **Liquidadas** em sete funções orçamentárias, normalizadas como uma porcentagem da Receita Líquida Real (% RL) [7, 11]. As funções analisadas são: **Saúde**, **Educação**, **Comunicação**, **Urbanismo**, **Habitação**, **Saneamento** e **Transporte** [7, 12].

*   **Variáveis de Ciclo:**
    *   `E_m1`: Ano pré-eleitoral (2017, 2021) [2].
    *   `E_0`: Ano eleitoral (2018, 2022) [2].
    *   `E_p1`: Ano pós-eleitoral (2019, 2023) [2].
*   **Variável de Controle:** `dlog_RL` (crescimento real da receita líquida no ano, por UF) [13].
*   **Tratamento de Dados:** Os dados foram submetidos a um processo de Winsorização (limites de 1% a 99%) para mitigar a influência de *outliers* extremos [14].

### Modelo Econométrico

A análise foi conduzida usando modelos de **Regressão em Dados em Painel** [1].
*   O **Teste de Hausman** rejeitou a hipótese nula para todas as funções, indicando que o **Modelo de Efeitos Fixos (FE)** é o mais apropriado (preferido) para capturar as características não observadas e invariáveis das Unidades Federativas (UFs) [15-20].
*   O **Teste de Wooldridge** rejeitou a hipótese nula de não haver autocorrelação serial, confirmando a necessidade de usar **erros-padrão clusterizados por UF** para garantir inferências válidas [21-23].

## 📊 Resultados e Principais Descobertas

O modelo final de Efeitos Fixos com erros clusterizados aponta evidências de comportamento oportunista nas despesas em torno dos anos eleitorais (coeficientes expressos em pontos percentuais - p.p.):

| Função Orçamentária | Coeficiente no Ano Eleitoral (`E_0`) | Nível de Significância | Observação | Modelo |
| :--- | :--- | :--- | :--- | :--- |
| **Educação** | **+0.940** | *** (p < 0.01) | Forte **aumento eleitoral** (gasto visível) [24, 25]. | FE [20] |
| **Transporte** | **+0.611** | *** (p < 0.01) | Aumento significativo no ano eleitoral [25, 26]. | FE [20] |
| **Urbanismo** | **+0.344** | *** (p < 0.01) | Aumento no ano eleitoral. Redução no ano pré-eleitoral (`E_m1`) [25, 27]. | FE [20] |
| **Comunicação** | -0.054 | ** (p < 0.05) | Redução no ano eleitoral e pré-eleitoral. | FE [25, 28] |
| **Saúde** | -0.008 | Não Significativo | Sem efeito eleitoral detectado. Redução significativa no ano pós-eleitoral (`E_p1`) [25, 29]. | FE [20] |
| **Saneamento** | +0.099 | Não Significativo | Redução significativa no ano pós-eleitoral (`E_p1`) [25, 30]. | FE [20] |

***Legenda de Significância:** *** p < 0.01; ** p < 0.05; * p < 0.1.

## 💻 Estrutura e Tecnologias

### Tecnologias Utilizadas

O projeto foi desenvolvido majoritariamente em Python, utilizando as seguintes bibliotecas [3]:
*   **Análise de Dados:** `pandas`, `numpy`
*   **Web Scraping/API:** `requests`, `io`
*   **Econometria de Painel:** `statsmodels`, `linearmodels` (`PanelOLS`, `RandomEffects`, `IV2SLS`) [3]
*   **Visualização:** `matplotlib.pyplot`
*   **Utilitários:** `tqdm`, `os`, `zipfile`, `re`, `time`, `scipy`

### Estrutura de Arquivos

Os *scripts* de processamento e análise estão organizados em blocos lógicos:
1.  **Coleta de Dados:** Blocos 1 (coleta SICONFI) e 4 (coleta IPCA) [3, 8].
2.  **Processamento e Deflacionamento:** Blocos 2 e 5 (tratamento dos dados de despesa de MG e deflacionamento) [5, 31].
3.  **Análise Descritiva:** Geração de gráficos e tabelas-síntese (como o panorama fiscal de MG) [32, 33].
4.  **Modelagem Econométrica:** Blocos 3.2, 3.3 e 3.4 (construção do painel nacional, testes de elegibilidade/balanceamento, construção das variáveis de ciclo, estimação e testes de robustez/autocorrelação) [1, 2, 34, 35].

**Para rodar o projeto localmente**, certifique-se de ter as bibliotecas listadas instaladas (ver o Bloco de Bibliotecas Auxiliares) e execute os blocos sequencialmente, pois a criação do painel nacional (`painel_funcoes_2015_2024.csv`) e dos deflatores é uma etapa prévia necessária para a modelagem [3, 13, 36].
