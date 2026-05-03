# Open Analytics Platform

> Projeto pessoal de Dados de ponta a ponta para aprender e demonstrar uma stack analítica moderna, gratuita ou de baixíssimo custo, reproduzível localmente e publicável no GitHub.

---

## Sumário

- [1. Visão geral](#1-visão-geral)
- [2. Objetivo do projeto](#2-objetivo-do-projeto)
- [3. O que você vai aprender](#3-o-que-você-vai-aprender)
- [4. Arquitetura](#4-arquitetura)
- [5. Stack técnica](#5-stack-técnica)
- [6. Estrutura do repositório](#6-estrutura-do-repositório)
- [7. Fases de execução](#7-fases-de-execução)
- [8. MVP esperado](#8-mvp-esperado)
- [9. Evoluções avançadas](#9-evoluções-avançadas)
- [10. Padrões técnicos](#10-padrões-técnicos)
- [11. Como usar dbt](#11-como-usar-dbt)
- [12. Como usar Airflow](#12-como-usar-airflow)
- [13. Como estruturar o dashboard React](#13-como-estruturar-o-dashboard-react)
- [14. Como encaixar Metabase](#14-como-encaixar-metabase)
- [15. Deploy gratuito do dashboard](#15-deploy-gratuito-do-dashboard)
- [16. Como rodar localmente](#16-como-rodar-localmente)
- [17. Sugestões de Makefile](#17-sugestões-de-makefile)
- [18. CI/CD com GitHub Actions](#18-cicd-com-github-actions)
- [19. Documentação esperada](#19-documentação-esperada)
- [20. Critérios de sucesso](#20-critérios-de-sucesso)

---

## 1. Visão geral

O **Open Analytics Platform** é um projeto pessoal para construir uma plataforma analítica moderna de ponta a ponta.

A ideia é simular, em escala reduzida, uma stack real de Analytics Engineering, contemplando:

```text
ingestão de dados → armazenamento/warehouse → transformação → qualidade de dados
→ orquestração → documentação → consumo em BI/dashboard → deploy
```

O foco principal **não é o domínio dos dados**. O dataset pode ser de vendas, marketing, financeiro, produto, operações, dados públicos, dados sintéticos ou qualquer outro tema.

O foco principal é o **core técnico da plataforma de dados**.

---

## 2. Objetivo do projeto

Criar um projeto de portfólio que demonstre domínio prático do ciclo completo de dados, usando ferramentas modernas, gratuitas ou de baixíssimo custo.

Ao final, o projeto deve ter:

- ambiente local reproduzível;
- ingestão com Python;
- armazenamento local com DuckDB;
- opção cloud com Snowflake;
- transformações com dbt Core;
- testes e documentação;
- orquestração com Airflow;
- dashboard em React;
- alternativa de BI com Metabase;
- CI/CD básico com GitHub Actions;
- deploy gratuito do dashboard;
- README forte e bem explicado.

---

## 3. O que você vai aprender

Ao executar este projeto, você deve aprender na prática:

- Como estruturar um projeto real de dados no GitHub.
- Como criar pipelines de ingestão com Python.
- Como usar Docker e Docker Compose para padronizar ambientes.
- Como usar DuckDB como warehouse analítico local.
- Como usar Snowflake como alternativa cloud/profissional.
- Como organizar transformações em camadas com dbt.
- Como criar testes de qualidade de dados.
- Como gerar documentação e lineage com dbt docs.
- Como orquestrar pipelines com Airflow.
- Como separar ingestão, transformação e consumo.
- Como exportar dados finais para JSON/CSV.
- Como criar um dashboard React consumindo arquivos estáticos.
- Como usar Metabase como alternativa de BI tradicional.
- Como configurar CI/CD básico com GitHub Actions.
- Como publicar um dashboard gratuitamente.
- Como documentar tecnicamente um projeto de portfólio.

---

## 4. Arquitetura

### 4.1 Arquitetura local usando DuckDB

```text
                ┌────────────────────┐
                │   Fonte de dados    │
                │ CSV / API / Faker   │
                └─────────┬──────────┘
                          │
                          ▼
                ┌────────────────────┐
                │ Python Ingestion    │
                │ scripts extract/load│
                └─────────┬──────────┘
                          │
                          ▼
                ┌────────────────────┐
                │ Data Lake Local     │
                │ data/raw/*.csv/json │
                └─────────┬──────────┘
                          │
                          ▼
                ┌────────────────────┐
                │ DuckDB Warehouse    │
                │ local .duckdb file  │
                └─────────┬──────────┘
                          │
                          ▼
                ┌────────────────────┐
                │ dbt Core            │
                │ staging/inter/marts │
                │ tests/docs/lineage  │
                └─────────┬──────────┘
                          │
             ┌────────────┴────────────┐
             ▼                         ▼
 ┌────────────────────┐      ┌────────────────────┐
 │ Export JSON/CSV    │      │ Metabase Optional   │
 │ public datasets    │      │ BI tradicional      │
 └─────────┬──────────┘      └────────────────────┘
           │
           ▼
 ┌────────────────────┐
 │ React Dashboard    │
 │ Static frontend    │
 └─────────┬──────────┘
           │
           ▼
 ┌────────────────────┐
 │ Deploy gratuito    │
 │ GitHub Pages /     │
 │ Vercel / Netlify   │
 └────────────────────┘
```

### 4.2 Arquitetura opcional usando Snowflake

```text
CSV/API/Synthetic Data
        │
        ▼
Python Ingestion
        │
        ▼
Snowflake Raw Schema
        │
        ▼
dbt Core
        │
        ├── staging
        ├── intermediate
        └── marts
        │
        ▼
Final Aggregated Tables
        │
        ▼
Export JSON/CSV
        │
        ▼
React Dashboard
```

> Regra importante: o dashboard React **não deve conectar diretamente no Snowflake ou em qualquer banco de dados**.  
> O fluxo recomendado é: modelos finais do dbt → exportação de arquivos JSON/CSV agregados → dashboard React consumindo arquivos estáticos.

---

## 5. Stack técnica

### Python

Usado para:

- ingestão de dados;
- consumo de APIs públicas;
- geração de dados sintéticos;
- carga em DuckDB;
- exportação de arquivos finais;
- scripts auxiliares.

Justificativa: Python é simples, amplamente usado em dados e ótimo para automações.

---

### Docker e Docker Compose

Usados para:

- subir Airflow;
- subir Metabase;
- padronizar ambiente local;
- evitar problemas de instalação;
- facilitar reprodução do projeto por qualquer pessoa.

Justificativa: mostra maturidade técnica e aproxima o projeto de ambientes reais.

---

### DuckDB

Usado como warehouse local.

Justificativa:

- gratuito;
- leve;
- roda localmente;
- excelente para projetos analíticos;
- integra bem com dbt;
- permite simular um warehouse sem custo cloud.

---

### Snowflake

Usado como modo cloud opcional.

Justificativa:

- muito usado em ambientes corporativos;
- bom para demonstrar conhecimento de cloud data warehouse;
- pode ser usado com trial/free credits;
- ajuda a entender diferenças entre ambiente local e cloud.

Snowflake **não é obrigatório para o MVP**.

---

### dbt Core

Usado para:

- transformações SQL;
- organização em camadas;
- testes;
- documentação;
- lineage;
- padronização da modelagem.

Justificativa: dbt é uma das ferramentas centrais de Analytics Engineering moderno.

---

### Airflow

Usado para:

- orquestrar ingestão;
- rodar dbt;
- exportar dados finais;
- simular pipeline de produção;
- acompanhar logs e dependências.

Justificativa: ensina DAGs, scheduling, logs, retries e execução controlada de pipelines.

---

### Metabase

Usado como BI complementar.

Justificativa:

- open-source;
- fácil de subir com Docker;
- simula consumo por ferramenta tradicional de BI;
- útil para comparar BI tradicional com dashboard customizado.

Metabase é complementar. A entrega principal é o dashboard React.

---

### React

Usado para construir o dashboard principal.

Justificativa:

- permite criar uma camada de consumo customizada;
- pode ser publicado gratuitamente;
- gera uma experiência visual moderna;
- pode ser desenvolvido com apoio de IA, mesmo que o desenvolvedor não domine React.

---

### GitHub

Usado para:

- versionamento;
- organização do projeto;
- issues;
- pull requests;
- README;
- portfólio.

---

### GitHub Actions

Usado para CI/CD básico:

- validar código Python;
- rodar dbt;
- rodar testes;
- buildar dashboard React;
- opcionalmente publicar no GitHub Pages.

---

## 6. Estrutura do repositório

Estrutura recomendada:

```text
open-analytics-platform/
│
├── README.md
├── Makefile
├── .env.example
├── .gitignore
├── docker-compose.yml
├── requirements.txt
├── pyproject.toml
│
├── data/
│   ├── raw/
│   ├── processed/
│   ├── exports/
│   └── sample/
│
├── ingestion/
│   ├── __init__.py
│   ├── extract.py
│   ├── load_duckdb.py
│   ├── load_snowflake.py
│   ├── generate_synthetic_data.py
│   └── config.py
│
├── warehouse/
│   └── local.duckdb
│
├── dbt/
│   ├── dbt_project.yml
│   ├── profiles.yml.example
│   ├── packages.yml
│   ├── models/
│   │   ├── sources.yml
│   │   ├── staging/
│   │   │   ├── stg_customers.sql
│   │   │   ├── stg_orders.sql
│   │   │   └── schema.yml
│   │   ├── intermediate/
│   │   │   ├── int_customer_orders.sql
│   │   │   └── schema.yml
│   │   └── marts/
│   │       ├── mart_daily_metrics.sql
│   │       ├── mart_customer_summary.sql
│   │       └── schema.yml
│   ├── macros/
│   ├── seeds/
│   ├── tests/
│   └── snapshots/
│
├── airflow/
│   ├── dags/
│   │   └── analytics_pipeline_dag.py
│   ├── logs/
│   └── plugins/
│
├── dashboard/
│   ├── package.json
│   ├── vite.config.js
│   ├── public/
│   │   └── data/
│   │       ├── daily_metrics.json
│   │       └── customer_summary.json
│   └── src/
│       ├── App.jsx
│       ├── main.jsx
│       ├── components/
│       ├── pages/
│       ├── services/
│       │   └── dataService.js
│       └── styles/
│
├── metabase/
│   └── README.md
│
├── scripts/
│   ├── run_ingestion.sh
│   ├── run_dbt.sh
│   ├── export_dashboard_data.py
│   └── reset_local_env.sh
│
├── docs/
│   ├── architecture.md
│   ├── data_model.md
│   ├── decisions.md
│   ├── setup.md
│   └── screenshots/
│
└── .github/
    └── workflows/
        ├── ci.yml
        └── deploy-dashboard.yml
```

---

## 7. Fases de execução

## Fase 1 — Setup inicial do repositório

### Objetivo

Criar a base do projeto, com estrutura organizada, controle de versão e documentação inicial.

### Atividades

- Criar o repositório no GitHub.
- Criar estrutura de pastas.
- Criar `.gitignore`.
- Criar `.env.example`.
- Criar README inicial.
- Criar Makefile.
- Criar ambiente Python.
- Definir se o primeiro dataset será CSV, API pública ou sintético.

### Entregáveis

- Repositório GitHub criado.
- Estrutura de pastas inicial.
- README com visão geral.
- Ambiente local funcionando.
- Primeiro commit organizado.

---

## Fase 2 — Ingestão de dados com Python

### Objetivo

Criar uma rotina simples e reproduzível para trazer dados para dentro do projeto.

### Atividades

- Escolher uma fonte de dados.
- Criar script de extração.
- Salvar dados em `data/raw`.
- Criar logs básicos.
- Criar validações simples.
- Criar script para carregar dados no DuckDB.

### Fluxo

```text
API/CSV/Faker
   ↓
Python extract
   ↓
data/raw
   ↓
DuckDB raw tables
```

### Entregáveis

- Script `extract.py`.
- Script `load_duckdb.py`.
- Dados salvos em `data/raw`.
- Tabelas raw criadas no DuckDB.
- Instrução no README de como rodar a ingestão.

---

## Fase 3 — Warehouse local com DuckDB

### Objetivo

Criar um ambiente analítico local usando DuckDB.

### Atividades

- Criar arquivo `warehouse/local.duckdb`.
- Criar schemas ou camadas lógicas.
- Carregar tabelas raw.
- Validar leitura via SQL.
- Documentar o motivo de usar DuckDB.

### Entregáveis

- Banco DuckDB local funcionando.
- Tabelas raw carregadas.
- Script de reset local.
- Exemplo de query documentado.

---

## Fase 4 — Transformações com dbt Core

### Objetivo

Modelar os dados em camadas usando dbt.

### Camadas recomendadas

```text
raw → staging → intermediate → marts
```

### Atividades

- Configurar `dbt_project.yml`.
- Configurar profile para DuckDB.
- Criar sources.
- Criar modelos staging.
- Criar modelos intermediate.
- Criar modelos marts.
- Criar testes básicos.
- Criar documentação dos modelos.

### Entregáveis

- Projeto dbt funcionando.
- Modelos SQL organizados por camada.
- Testes dbt criados.
- `dbt run` funcionando.
- `dbt test` funcionando.
- `dbt docs generate` funcionando.

---

## Fase 5 — Qualidade de dados

### Objetivo

Adicionar confiança ao pipeline.

### Testes sugeridos

- `not_null`
- `unique`
- `accepted_values`
- `relationships`
- testes customizados
- validação de volume mínimo
- validação de datas futuras
- validação de métricas negativas

### Exemplo

```yaml
models:
  - name: mart_daily_metrics
    columns:
      - name: metric_date
        tests:
          - not_null
          - unique

      - name: total_revenue
        tests:
          - not_null
```

### Entregáveis

- Testes dbt documentados.
- Falhas esperadas explicadas.
- README explicando como rodar qualidade.
- Evidência de execução dos testes.

---

## Fase 6 — Documentação e lineage com dbt

### Objetivo

Gerar documentação técnica navegável.

### Atividades

- Adicionar descrição em `schema.yml`.
- Documentar sources.
- Documentar modelos.
- Documentar colunas principais.
- Gerar documentação com `dbt docs generate`.
- Servir documentação local com `dbt docs serve`.
- Tirar prints do lineage.

### Entregáveis

- Documentação dbt gerada.
- Prints em `docs/screenshots`.
- README explicando lineage.
- Descrição clara das camadas.

---

## Fase 7 — Orquestração com Airflow

### Objetivo

Criar uma DAG que execute o pipeline completo.

### Pipeline sugerido

```text
start
  ↓
extract_data
  ↓
load_raw_to_duckdb
  ↓
dbt_run
  ↓
dbt_test
  ↓
export_dashboard_data
  ↓
end
```

### Atividades

- Subir Airflow com Docker Compose.
- Criar DAG `analytics_pipeline_dag.py`.
- Criar tasks usando `BashOperator` ou `PythonOperator`.
- Rodar ingestão.
- Rodar dbt.
- Rodar exportação.
- Validar logs.

### Entregáveis

- Airflow subindo localmente.
- DAG criada.
- Pipeline executando ponta a ponta.
- Prints da DAG.
- Explicação no README.

---

## Fase 8 — Exportação dos dados finais

### Objetivo

Gerar arquivos estáticos para consumo no dashboard React.

### Por que fazer isso?

Porque o dashboard React não deve conectar diretamente no banco ou Snowflake.

Isso evita:

- exposição de credenciais;
- custo desnecessário;
- complexidade de backend;
- problemas de segurança.

### Fluxo recomendado

```text
dbt marts
   ↓
Python export script
   ↓
dashboard/public/data/*.json
   ↓
React dashboard
```

### Entregáveis

- Script `export_dashboard_data.py`.
- Arquivos JSON/CSV gerados.
- Dados finais versionáveis ou reproduzíveis.
- Explicação da decisão técnica.

---

## Fase 9 — Dashboard React

### Objetivo

Construir um dashboard customizado consumindo arquivos JSON/CSV estáticos.

### O dashboard deve conter

- Página inicial.
- Cards de KPIs.
- Gráfico de linha.
- Gráfico de barras.
- Tabela analítica.
- Filtros básicos.
- Texto explicando as métricas.
- Link para GitHub.
- Layout responsivo.

### Estrutura sugerida

```text
dashboard/src/
├── App.jsx
├── main.jsx
├── components/
│   ├── KpiCard.jsx
│   ├── LineChart.jsx
│   ├── BarChart.jsx
│   ├── DataTable.jsx
│   └── FilterPanel.jsx
├── pages/
│   └── Dashboard.jsx
├── services/
│   └── dataService.js
└── styles/
```

### Bibliotecas sugeridas

- React
- Vite
- Recharts
- Tailwind CSS
- PapaParse, se usar CSV
- Lodash, opcional

### Entregáveis

- Dashboard React funcionando localmente.
- Dados carregando de arquivos estáticos.
- Layout limpo.
- Instruções de execução.
- Prints no README.

---

## Fase 10 — Metabase como alternativa

### Objetivo

Mostrar que os dados também podem ser consumidos por uma ferramenta tradicional de BI.

### Como encaixar

Metabase pode ser usado para conectar no DuckDB ou em uma camada intermediária, dependendo da facilidade técnica.

Caso a conexão direta com DuckDB gere complexidade, pode-se usar uma alternativa como Postgres local apenas para consumo no Metabase.

### Papel do Metabase no projeto

Metabase não é o produto principal. Ele serve para demonstrar:

- consumo self-service;
- exploração visual;
- comparação entre BI tradicional e dashboard customizado;
- facilidade para usuários não técnicos.

### Entregáveis

- Metabase subindo via Docker Compose.
- Pelo menos um dashboard simples.
- Print no README.
- Explicação de quando usar Metabase versus React.

---

## Fase 11 — GitHub Actions

### Objetivo

Criar um CI/CD básico para validar o projeto.

### Checks recomendados

- Instalar dependências Python.
- Validar lint Python.
- Rodar testes dbt.
- Rodar `dbt compile`.
- Buildar dashboard React.
- Opcionalmente publicar o dashboard.

### Pipeline conceitual

```text
push/pull request
   ↓
setup python
   ↓
install dependencies
   ↓
dbt deps
   ↓
dbt compile
   ↓
dbt test
   ↓
setup node
   ↓
npm install
   ↓
npm run build
```

### Entregáveis

- Workflow `.github/workflows/ci.yml`.
- CI rodando no GitHub.
- Badge no README.
- Evidência de build bem-sucedido.

---

## Fase 12 — Deploy gratuito do dashboard

### Objetivo

Publicar o dashboard React para acesso público.

### Opções

#### Vercel

Boa opção para React/Vite.

Vantagens:

- deploy simples;
- preview automático;
- boa experiência de frontend;
- gratuito para projetos pequenos.

#### GitHub Pages

Boa opção se o projeto for simples e totalmente estático.

Vantagens:

- gratuito;
- integrado ao GitHub;
- bom para portfólio.

#### Netlify

Também excelente para sites estáticos.

Vantagens:

- fácil configuração;
- deploy por GitHub;
- gratuito para projetos pequenos.

#### AWS Amplify

Boa opção se quiser demonstrar familiaridade com cloud.

Vantagens:

- mais próximo de ambiente corporativo;
- integra bem com GitHub;
- pode ser interessante para currículo.

### Recomendação

Começar com **Vercel** ou **GitHub Pages**.

### Entregáveis

- Dashboard publicado.
- Link no README.
- Print da aplicação.
- Explicação do processo de deploy.

---

## 8. MVP esperado

O MVP deve ser simples, mas completo.

### MVP obrigatório

- Repositório GitHub organizado.
- Ingestão com Python.
- Dados salvos em `data/raw`.
- DuckDB local funcionando.
- dbt com:
  - sources;
  - staging;
  - intermediate;
  - marts;
  - testes;
  - documentação.
- Airflow com DAG executando:
  - ingestão;
  - carga;
  - dbt run;
  - dbt test;
  - exportação.
- Dashboard React consumindo JSON/CSV estático.
- Deploy gratuito do dashboard.
- README forte com arquitetura, execução e prints.

### MVP mínimo em termos de dados

Mesmo que o dataset seja simples, deve existir:

- pelo menos 3 tabelas raw;
- pelo menos 3 modelos staging;
- pelo menos 1 modelo intermediate;
- pelo menos 2 modelos marts;
- pelo menos 5 testes dbt;
- pelo menos 1 dashboard com KPIs e gráficos.

---

## 9. Evoluções avançadas

Depois do MVP, o projeto pode evoluir com os seguintes tópicos.

### Engenharia de dados

- Ingestão incremental.
- Particionamento lógico.
- Controle de carga por data.
- Logs estruturados.
- Retry em falhas.
- Validação com Great Expectations ou Soda.
- Observabilidade básica.

### dbt

- Snapshots.
- Macros customizadas.
- Seeds.
- Exposures.
- Métricas padronizadas.
- Testes customizados.
- Documentação mais rica.

### Snowflake

- Modo cloud completo.
- Separação por schemas:
  - raw;
  - staging;
  - intermediate;
  - marts.
- Uso de roles e warehouses.
- Carga via stage.
- Comparação DuckDB versus Snowflake.

### Airflow

- DAG parametrizada.
- Backfill.
- Sensors.
- Alertas.
- Separação por ambientes.
- TaskGroups.

### Dashboard

- Filtros mais avançados.
- Múltiplas páginas.
- Drill-down.
- Comparativo temporal.
- Tema dark/light.
- Componentização melhor.
- Testes de frontend.

### CI/CD

- Deploy automático.
- Validação de PR.
- Lint SQL.
- Lint Python.
- Build do dashboard.
- Publicação automática no GitHub Pages.

---

## 10. Padrões técnicos

## 10.1 Nomenclatura

### Arquivos Python

Usar `snake_case`:

```text
extract_orders.py
load_duckdb.py
export_dashboard_data.py
```

### Modelos dbt

Padrão sugerido:

```text
stg_<source>__<entity>.sql
int_<business_process>.sql
mart_<subject_area>.sql
```

Exemplos:

```text
stg_app__customers.sql
stg_app__orders.sql
int_customer_orders.sql
mart_daily_metrics.sql
mart_customer_summary.sql
```

### Colunas

Usar `snake_case`:

```text
customer_id
order_id
created_at
updated_at
total_amount
order_status
```

### Chaves

Usar:

```text
<entity>_id
```

Exemplos:

```text
customer_id
product_id
order_id
```

---

## 10.2 Camadas de dados

### Raw

Dados como vieram da origem.

Características:

- pouca ou nenhuma transformação;
- preservação dos dados brutos;
- base para rastreabilidade;
- camada não ideal para consumo direto.

---

### Staging

Primeira camada limpa.

Responsável por:

- renomear colunas;
- fazer cast de tipos;
- padronizar datas;
- remover duplicidades óbvias;
- criar colunas técnicas;
- aplicar filtros mínimos.

---

### Intermediate

Camada de regra de negócio intermediária.

Responsável por:

- joins;
- enriquecimentos;
- deduplicações mais complexas;
- cálculos reutilizáveis;
- preparação dos marts.

---

### Marts

Camada final de consumo.

Responsável por:

- métricas finais;
- agregações;
- tabelas para BI;
- tabelas para exportação JSON/CSV;
- modelos fáceis de consumir.

---

## 10.3 Testes mínimos

Todo modelo importante deve ter:

- `not_null` em chaves;
- `unique` quando aplicável;
- `relationships` entre entidades;
- `accepted_values` para status/categorias;
- testes de métricas negativas.

Exemplo:

```yaml
models:
  - name: stg_orders
    columns:
      - name: order_id
        tests:
          - not_null
          - unique

      - name: customer_id
        tests:
          - not_null

      - name: order_status
        tests:
          - accepted_values:
              values: ['pending', 'paid', 'cancelled', 'refunded']
```

---

## 10.4 Logs

Scripts Python devem ter logs simples e claros:

```text
[INFO] Starting extraction
[INFO] Extracted 10,000 rows
[INFO] Saved file data/raw/orders.csv
[INFO] Loaded table raw_orders into DuckDB
[ERROR] Failed to connect to API
```

Evitar apenas `print()` solto. Preferir o módulo `logging`.

---

## 10.5 Variáveis de ambiente

Criar `.env.example`:

```env
ENVIRONMENT=local

DUCKDB_PATH=warehouse/local.duckdb

SNOWFLAKE_ACCOUNT=
SNOWFLAKE_USER=
SNOWFLAKE_PASSWORD=
SNOWFLAKE_ROLE=
SNOWFLAKE_WAREHOUSE=
SNOWFLAKE_DATABASE=
SNOWFLAKE_SCHEMA=

AIRFLOW_UID=50000
```

Nunca versionar `.env` real.

---

## 11. Como usar dbt

O dbt deve ser o coração da transformação analítica.

### Responsabilidades do dbt

- Ler tabelas raw.
- Criar camada staging.
- Criar camada intermediate.
- Criar camada marts.
- Executar testes.
- Gerar documentação.
- Gerar lineage.
- Padronizar a modelagem.

### Comandos principais

```bash
dbt debug
dbt deps
dbt seed
dbt run
dbt test
dbt docs generate
dbt docs serve
```

### Estrutura de modelos

```text
dbt/models/
├── sources.yml
├── staging/
├── intermediate/
└── marts/
```

### Exemplo de source

```yaml
sources:
  - name: raw
    database: local
    schema: raw
    tables:
      - name: customers
      - name: orders
      - name: products
```

### Exemplo de staging

```sql
select
    cast(id as integer) as customer_id,
    cast(name as varchar) as customer_name,
    cast(email as varchar) as email,
    cast(created_at as timestamp) as created_at
from {{ source('raw', 'customers') }}
```

### Exemplo de mart

```sql
select
    date_trunc('day', order_created_at) as order_date,
    count(distinct order_id) as total_orders,
    count(distinct customer_id) as active_customers,
    sum(order_amount) as total_revenue
from {{ ref('int_customer_orders') }}
group by 1
```

---

## 12. Como usar Airflow

Airflow deve orquestrar o fluxo completo, não concentrar a regra de negócio.

### O que o Airflow deve fazer

- Chamar scripts Python.
- Rodar comandos dbt.
- Rodar testes.
- Exportar dados finais.
- Registrar logs.
- Mostrar dependências entre etapas.

### O que o Airflow não deve fazer

- Não deve conter regra de negócio complexa.
- Não deve substituir o dbt.
- Não deve ter SQL gigante dentro da DAG.
- Não deve virar lugar de transformação analítica.

### DAG recomendada

```text
analytics_pipeline_dag
│
├── extract_data
├── load_raw_to_duckdb
├── dbt_run
├── dbt_test
├── generate_dbt_docs
└── export_dashboard_data
```

### Exemplo conceitual

```python
extract_data >> load_raw_to_duckdb >> dbt_run >> dbt_test >> export_dashboard_data
```

### Entregável esperado

Você deve conseguir abrir a UI do Airflow e mostrar a DAG executando com sucesso de ponta a ponta.

---

## 13. Como estruturar o dashboard React

O dashboard React deve ser simples, estático e bem apresentado.

### Regra principal

O React deve consumir arquivos estáticos:

```text
dashboard/public/data/*.json
```

Não deve conectar direto no banco.

### Exemplo de arquivos exportados

```text
dashboard/public/data/
├── daily_metrics.json
├── category_performance.json
├── customer_summary.json
└── metadata.json
```

### Estrutura visual sugerida

```text
Header
  └── Nome do projeto + link GitHub

Filters
  └── Data / categoria / status

KPI Cards
  ├── Total Revenue
  ├── Total Orders
  ├── Active Customers
  └── Average Ticket

Charts
  ├── Revenue over time
  ├── Orders by category
  └── Status distribution

Table
  └── Detailed metrics

Footer
  └── Stack utilizada
```

### Componentes sugeridos

```text
KpiCard
LineChart
BarChart
DataTable
FilterPanel
Layout
Header
Footer
```

### O que avaliar no dashboard

- Está funcional?
- Está visualmente apresentável?
- É responsivo?
- Os dados vêm dos marts?
- Existe explicação das métricas?
- Existe link para o GitHub?
- O deploy está funcionando?

---

## 14. Como encaixar Metabase

Metabase entra como entrega complementar.

### Objetivo

Mostrar que a mesma base analítica pode ser consumida por uma ferramenta de BI tradicional.

### Possíveis abordagens

#### Opção 1 — Metabase conectado a banco local

Usar Postgres como camada de consumo caso conectar diretamente no DuckDB seja difícil.

Fluxo:

```text
dbt marts
   ↓
export/load to Postgres
   ↓
Metabase
```

#### Opção 2 — Metabase conectado ao Snowflake

Se o modo cloud for ativado, conectar Metabase ao Snowflake.

Fluxo:

```text
Snowflake marts
   ↓
Metabase
```

### Entrega mínima

- Subir Metabase no Docker.
- Criar uma conexão.
- Criar 1 dashboard simples.
- Colocar print no README.

### Mensagem importante

Metabase é uma alternativa de consumo. A entrega principal continua sendo o dashboard React publicado.

---

## 15. Deploy gratuito do dashboard

### Opção recomendada: Vercel

Passos:

1. Criar conta na Vercel.
2. Conectar com GitHub.
3. Selecionar o repositório.
4. Definir pasta do projeto como `dashboard`.
5. Definir build command:

```bash
npm run build
```

6. Definir output directory:

```bash
dist
```

7. Publicar.

---

### Alternativa: GitHub Pages

Boa para projeto totalmente estático.

Comando de build:

```bash
npm run build
```

Depois, publicar a pasta `dist`.

---

### Alternativa: Netlify

Configuração típica:

```text
Base directory: dashboard
Build command: npm run build
Publish directory: dashboard/dist
```

---

### Entregável

No README, incluir:

```text
Live dashboard: https://...
```

---

## 16. Como rodar localmente

> Esta seção deve ser ajustada conforme o projeto real for sendo implementado.

### 16.1 Clonar o repositório

```bash
git clone https://github.com/<usuario>/<repositorio>.git
cd <repositorio>
```

### 16.2 Criar ambiente Python

```bash
python -m venv .venv
source .venv/bin/activate
```

No Windows:

```bash
.venv\Scripts\activate
```

### 16.3 Instalar dependências

```bash
pip install -r requirements.txt
```

### 16.4 Configurar variáveis de ambiente

```bash
cp .env.example .env
```

Depois, preencher os valores necessários no arquivo `.env`.

### 16.5 Subir serviços com Docker

```bash
docker compose up -d
```

### 16.6 Rodar ingestão

```bash
make ingest
```

Ou:

```bash
python ingestion/extract.py
python ingestion/load_duckdb.py
```

### 16.7 Rodar dbt

```bash
make dbt-run
make dbt-test
```

Ou:

```bash
cd dbt
dbt run
dbt test
```

### 16.8 Gerar documentação dbt

```bash
cd dbt
dbt docs generate
dbt docs serve
```

### 16.9 Exportar dados para o dashboard

```bash
make export
```

Ou:

```bash
python scripts/export_dashboard_data.py
```

### 16.10 Rodar dashboard React

```bash
cd dashboard
npm install
npm run dev
```

---

## 17. Sugestões de Makefile

```makefile
install:
	pip install -r requirements.txt

up:
	docker compose up -d

down:
	docker compose down

reset:
	bash scripts/reset_local_env.sh

ingest:
	python ingestion/extract.py
	python ingestion/load_duckdb.py

dbt-debug:
	cd dbt && dbt debug

dbt-run:
	cd dbt && dbt run

dbt-test:
	cd dbt && dbt test

dbt-docs:
	cd dbt && dbt docs generate && dbt docs serve

export:
	python scripts/export_dashboard_data.py

pipeline:
	make ingest
	make dbt-run
	make dbt-test
	make export

dashboard-install:
	cd dashboard && npm install

dashboard-dev:
	cd dashboard && npm run dev

dashboard-build:
	cd dashboard && npm run build

airflow:
	docker compose up airflow-webserver airflow-scheduler -d

metabase:
	docker compose up metabase -d
```

---

## 18. CI/CD com GitHub Actions

O projeto deve ter pelo menos um workflow básico de CI.

### Objetivo

Validar automaticamente se o projeto continua funcionando a cada push ou pull request.

### Checks recomendados

- Instalar dependências Python.
- Rodar lint Python.
- Instalar dependências dbt.
- Rodar `dbt compile`.
- Rodar `dbt test`.
- Instalar dependências Node.
- Buildar dashboard React.

### Exemplo conceitual

```text
push/pull request
   ↓
setup python
   ↓
install dependencies
   ↓
dbt deps
   ↓
dbt compile
   ↓
dbt test
   ↓
setup node
   ↓
npm install
   ↓
npm run build
```

### Entregáveis

- Arquivo `.github/workflows/ci.yml`.
- CI rodando no GitHub.
- Badge no README.
- Evidência de build bem-sucedido.

---

## 19. Documentação esperada

O README é parte central do projeto. Ele precisa vender bem o trabalho.

### O que não pode faltar

- Diagrama de arquitetura.
- Explicação das ferramentas.
- Comandos para rodar.
- Prints do Airflow.
- Prints do dbt docs.
- Prints do dashboard.
- Link do dashboard publicado.
- Explicação do motivo de não conectar React direto no banco.
- Explicação DuckDB local versus Snowflake cloud.
- Critérios de sucesso.

### Estrutura extra recomendada para a pasta `docs/`

```text
docs/
├── architecture.md
├── data_model.md
├── decisions.md
├── setup.md
└── screenshots/
    ├── airflow_dag.png
    ├── dbt_lineage.png
    ├── dashboard_home.png
    └── metabase_dashboard.png
```

### Decisões técnicas que devem ser explicadas

Criar um arquivo `docs/decisions.md` contendo respostas para perguntas como:

- Por que DuckDB foi escolhido para o modo local?
- Por que Snowflake é opcional?
- Por que o dashboard React consome arquivos estáticos?
- Por que Airflow é usado apenas para orquestração?
- Por que dbt concentra a transformação?
- Quando usar Metabase e quando usar React?

---

## 20. Critérios de sucesso

O projeto pode ser considerado bem entregue se atender aos critérios abaixo.

### Critérios técnicos

- O ambiente sobe localmente com Docker.
- A ingestão roda com Python.
- Os dados são carregados no DuckDB.
- O dbt executa transformações em camadas.
- Existem testes dbt funcionando.
- A documentação dbt é gerada.
- O Airflow orquestra o pipeline.
- Os dados finais são exportados para JSON/CSV.
- O dashboard React consome dados estáticos.
- O dashboard está publicado gratuitamente.
- O repositório tem CI básico com GitHub Actions.

### Critérios de organização

- O repositório está limpo.
- O README é completo.
- As pastas seguem uma estrutura lógica.
- As decisões técnicas estão documentadas.
- Existe `.env.example`.
- Não há credenciais expostas.
- Existem prints e evidências de funcionamento.

### Critérios de aprendizado

Ao final, você deve conseguir explicar:

- Por que usar DuckDB localmente.
- Quando Snowflake faria sentido.
- Qual é o papel do dbt.
- Qual é o papel do Airflow.
- Diferença entre raw, staging, intermediate e marts.
- Como os testes dbt aumentam a confiança nos dados.
- Por que o React consome arquivos estáticos.
- Como o deploy foi feito.
- Como o pipeline roda de ponta a ponta.

### Critério final de portfólio

Você deve conseguir dizer em uma entrevista:

> “Eu construí uma plataforma analítica moderna de ponta a ponta, com ingestão em Python, warehouse local em DuckDB, transformações com dbt, testes de qualidade, orquestração com Airflow, documentação automática, dashboard em React e deploy gratuito. Também deixei a arquitetura preparada para rodar em Snowflake como modo cloud opcional.”

---

## Direção final

A entrega não precisa começar complexa. O mais importante é fazer o ciclo completo funcionar primeiro.

Ordem recomendada de prioridade:

```text
1. Rodar local
2. Ingerir dados
3. Transformar com dbt
4. Testar com dbt
5. Orquestrar com Airflow
6. Exportar dados finais
7. Criar dashboard React
8. Publicar dashboard
9. Documentar muito bem
10. Evoluir com Snowflake e Metabase
```

O projeto não será avaliado pela complexidade do dataset.

Ele será avaliado pela clareza da arquitetura, organização do repositório, reprodutibilidade do ambiente e capacidade de explicar as decisões técnicas.

---

## Próximos passos sugeridos

1. Criar o repositório no GitHub.
2. Copiar este arquivo como `README.md`.
3. Criar a estrutura inicial de pastas.
4. Fazer o primeiro commit.
5. Começar pela ingestão local com Python e DuckDB.
