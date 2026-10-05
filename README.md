# Inteligência de Mercado: Dimensionamento de Demanda (TAM/SAM/SOM) e Expansão Territorial no Varejo Farmacêutico (SP)

## 1. Sumário Executivo

Este projeto desenvolveu uma solução analítica de inteligência de mercado orientada a identificar áreas de baixa saturação concorrencial (*white spaces*) no varejo farmacêutico do Estado de São Paulo. Integrando microdados públicos da RAIS/CAGED com dados demográficos e hierárquicos do Censo 2022 (IBGE), a análise mapeou oportunidades com base no dimensionamento de mercado (TAM, SAM e SOM). O pipeline automatizou a extração e o tratamento dos 645 municípios paulistas em um Data Warehouse na nuvem (Google BigQuery), modelado em esquema estrela de 6 tabelas (Kimball), alimentando relatórios executivos para redução de risco em decisões de CAPEX de expansão física.

## 2. Problema de Negócio e Perguntas Norteadoras

Redes varejistas frequentemente enfrentam desaceleração de margens e saturação competitiva na capital e na Região Metropolitana de São Paulo (RMSP). Investimentos na abertura de filiais no interior demandam uma validação quantitativa que una concorrência instalada a potencial demográfico.

* **Qual é a taxa real de saturação farmacêutica por mil habitantes em cada microrregião de SP?**
* **Quais municípios polos reúnem baixa concorrência relativa e alto mercado endereçável?**
* **Como desenhar uma arquitetura de dados escalável para suportar decisões contínuas de inteligência comercial?**

## 3. Arquitetura da Solução e Modelagem Dimensional

A ingestão e o processamento foram construídos em Python, persistidos diretamente no Google BigQuery e modelados segundo a metodologia de Ralph Kimball (1 Fato e 5 Dimensões):

```text
                    [ dim_tempo ]
                          |
                          v
[ dim_regiao ] --> [ fato_densidade_varejo ] <-- [ dim_cnae ]
                      ^               ^
                      |               |
            [ dim_municipio ]   [ dim_perfil_socioeconomico ]
```

* **`fato_densidade_varejo`:** Registros quantitativos agregados por município, com métricas de população, estoque de estabelecimentos concorrentes (CNAE 4771-7/01) e dimensionamento financeiro (TAM, SAM e SOM).
* **`dim_municipio`:** Cadastro de códigos IBGE e nomes dos 645 municípios paulistas.
* **`dim_regiao`:** Hierarquia territorial com mesorregiões, microrregiões e classificação de macrocluster (Metropolitana vs. Interior/Litoral).
* **`dim_cnae`:** Categorização de atividade econômica e setor estratégico.
* **`dim_tempo`:** Controle de safras (RAIS 2021 e Censo 2022).
* **`dim_perfil_socioeconomico`:** Faixas de porte populacional e potencial de consumo.

## 4. Engenharia de Variáveis e Dimensionamento de Mercado

* **Densidade Concorrencial:** Estabelecimentos ativos por 1.000 habitantes.
* **TAM (Total Addressable Market):** Demanda municipal teórica total (População × R$ 850,00 per capita anual em saúde e higiene).
* **SAM (Serviceable Available Market):** Mercado endereçável restrito a praças viáveis para redes estruturadas (população ≥ 50.000 habitantes).
* **SOM (Serviceable Obtainable Market):** Projeção de captura inicial conservadora de 4% da demanda do SAM local.

## 5. Estrutura do Repositório e Execução

```text
market-sizing-pharma-expansion/
├── 01_pipeline_etl_dimensionamento_mercado.ipynb  # Caderno didático exploratório
├── README.md                                      # Documentação executiva
├── requirements.txt                               # Dependências do ambiente Python
├── scripts/                                       # Scripts executáveis de automação
│   └── etl_ibge_rais.py
├── models/                                        # DDL e scripts relacionais
│   └── schema_kimball.sql
└── bi/                                            # Arquivo do dashboard executivo
    └── dashboard_expansao.pbix
```

### Instruções para Reprodução

1. Clone o repositório:

```bash
git clone https://github.com/SEU_USUARIO/market-sizing-pharma-expansion.git
cd market-sizing-pharma-expansion
```

2. Instale os requisitos:

```bash
pip install -r requirements.txt
```

3. Execute o pipeline de produção:

```bash
python scripts/etl_ibge_rais.py
```

> **Nota:** é necessário configurar um projeto ativo no Google Cloud Console, com acesso à BigQuery API, para a autenticação.
