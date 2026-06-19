# 🛒 Big Data & Analytics — Redução de Abandono de Carrinho
> Projeto acadêmico — FIAP | Tecnologia em Sistemas para Internet | Fase 6 — Big Data & Analytics | 2º Semestre 2025

---

## 📋 Sobre o Projeto

Este projeto propõe uma **Arquitetura Analítica Big Data** completa para resolver um dos maiores desafios do e-commerce: o abandono de carrinho de compras.

O case é baseado na empresa fictícia **Melhores Compras**, que enfrenta taxas de abandono superiores a **70%** — o que representa perda de aproximadamente 70% das oportunidades de venda na última etapa do funil de conversão.

A solução foi desenhada para permitir:
- Identificação em **tempo real** de usuários em risco de abandono
- Acionamento automático de **campanhas de recuperação personalizadas**
- Construção de **perfis comportamentais** de usuários anônimos e cadastrados
- **Dashboards e KPIs** operacionais para acompanhamento da taxa de conversão
- Alimentação de modelos de **Machine Learning** para previsão de abandono

---

## 🏗️ Arquitetura da Solução

A solução segue o modelo **Lambda Architecture**, combinando processamento **batch** (análises históricas e treinamento de modelos) e **streaming** (acionamentos em tempo real).

```
┌─────────────────────────────────────────────────────────────────┐
│                      DATA SOURCES                               │
│  E-commerce · CRM Salesforce · ERP Oracle · App Mobile         │
│  Google Ads · Meta Ads · Mailchimp · Redes Sociais             │
└────────────────────────┬────────────────────────────────────────┘
                         │ Streaming (Kafka / Kinesis)
                         │ Batch (Sqoop / APIs REST)
                         ▼
┌─────────────────────────────────────────────────────────────────┐
│                       DATA LAKE (AWS S3)                        │
│                                                                 │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐  │
│  │   RAW Zone   │  │ TRUSTED Zone │  │    REFINED Zone      │  │
│  │ Dados brutos │→ │ Dados limpos │→ │ Dados enriquecidos   │  │
│  │ JSON/Parquet │  │   Parquet    │  │ Delta Lake (ACID)    │  │
│  └──────────────┘  └──────────────┘  └──────────────────────┘  │
└────────────────────────┬────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────────┐
│                  DATA COMPUTE / ANALYSIS                        │
│                                                                 │
│  Batch: Apache Spark + Hive + Airflow (DAGs diários)           │
│  Streaming: Spark Streaming + Kafka Streams (janelas 5-15 min) │
│  ML: Random Forest / XGBoost via AWS SageMaker                 │
└────────────────────────┬────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────────┐
│                     DATA PRESENTATION                           │
│                                                                 │
│  Dashboards: Apache Superset / Power BI                        │
│  Recuperação: E-mail (Mailchimp) · Push (Firebase) · SMS (SNS) │
│  MLOps: AWS SageMaker · API REST para personalização           │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🧰 Stack Tecnológica

### Ingestão & Mensageria
| Tecnologia | Função |
|---|---|
| Apache Kafka | Captura de eventos de clickstream em tempo real |
| AWS Kinesis | Ingestão de eventos do app mobile |
| Apache Sqoop | Ingestão batch do ERP Oracle |
| Apache Airflow | Orquestração de pipelines batch (DAGs) |

### Armazenamento
| Tecnologia | Função |
|---|---|
| Amazon S3 | Data Lake principal (Raw / Trusted / Refined) |
| Apache Hadoop HDFS | Armazenamento distribuído on-premise |
| Delta Lake | Transações ACID e upserts na camada Refined |
| Apache HBase | Perfis de usuário com acesso de baixa latência |

### Processamento & Análise
| Tecnologia | Função |
|---|---|
| Apache Spark | ETL, transformações e Spark Streaming |
| Apache Hive | SQL sobre o Data Lake para análises batch |
| Apache Zookeeper | Coordenação dos serviços distribuídos |
| Random Forest / XGBoost | Modelo preditivo de probabilidade de abandono |
| AWS SageMaker | MLOps — ciclo de vida dos modelos de ML |

### Apresentação
| Tecnologia | Função |
|---|---|
| Apache Superset / Power BI | Dashboards e KPIs operacionais |
| Mailchimp API | Campanhas de e-mail de recuperação |
| Firebase Cloud Messaging | Push notifications no app mobile |
| AWS SNS | Disparo de SMS |

---

## 🔄 Fluxo de Dados — Cenário de Abandono em Tempo Real

```
1. Usuário adiciona produto ao carrinho
         ↓
2. Evento add_to_cart publicado no Kafka Topic: clickstream.events
         ↓
3. Usuário fica inativo por 15 min sem iniciar checkout
         ↓
4. Spark Streaming detecta inatividade via janela temporal
         ↓
5. Modelo de ML calcula probabilidade de abandono = 85%
         ↓
6. Limiar de 70% ultrapassado → alerta no Kafka Topic: abandonment.alerts
         ↓
7. Microserviço consulta perfil do usuário no HBase
         ↓
8. E-mail personalizado enviado com lembrete + cupom de 10%
         ↓
9. Evento registrado no Data Lake para análise de efetividade
         ↓
10. Dashboard atualizado com métrica de carrinhos em recuperação
```

---

## 📊 Fontes de Dados Mapeadas

| Origem | Formato | Velocidade | Volume estimado |
|---|---|---|---|
| Plataforma E-commerce (Clickstream) | JSON (Avro) | Streaming real-time | ~500 mil eventos/dia |
| CRM Salesforce | API REST (JSON) | Batch diário | ~50 mil registros/dia |
| ERP Oracle | Tabela relacional | Batch diário | ~10 mil registros/dia |
| App Mobile (iOS/Android) | JSON (push events) | Streaming real-time | ~200 mil eventos/dia |
| Google Ads / Meta Ads | CSV / API REST | Batch diário | ~5 mil registros/dia |
| E-mail Marketing (Mailchimp) | API REST | Batch diário | ~2 mil registros/dia |
| Redes Sociais | JSON | Near real-time (15 min) | ~1 mil posts/dia |

---

## 📈 KPIs e Dashboards

- Taxa de abandono de carrinho por categoria, canal e período
- Taxa de recuperação de carrinhos abandonados
- ROI de campanhas de recuperação (e-mail, push, SMS)
- Ranking de produtos mais abandonados
- Mapa de calor das etapas do funil de conversão
- Segmentação RFM de clientes

---

## 🎯 Resultado Esperado

Com a solução implementada, a Melhores Compras estaria preparada para:

- Reduzir a taxa de abandono em até **20–30% no primeiro ano**
- Aumentar a taxa de conversão com **personalização orientada por dados**
- Construir uma **visão 360° do cliente** integrando múltiplas fontes
- Criar a base analítica para modelos preditivos cada vez mais sofisticados

---

## 👥 Equipe

| Nome | RM |
|---|---|
| Yan Reis | RM566698 |
| Gabriel Rodriguez | RM566648 |
| Giovanni Torres | RM566638 |
| Roberto Nacário | RM566734 |
| Thierry Alves | RM567243 |

> **Instituição:** FIAP — Faculdade de Informática e Administração Paulista  
> **Curso:** Tecnologia em Sistemas para Internet — 1º Ano  
> **Disciplina:** Big Data & Analytics — Fase 6  
> **Período:** 2º Semestre de 2025

---

## 📁 Estrutura do Repositório

```
bigdata-abandono-carrinho/
│
├── README.md                          # Este arquivo
└── docs/
    └── MelhoresCompras_BigData_Fase6.docx   # Documentação completa do projeto
```

---

*Projeto acadêmico desenvolvido para fins educacionais no contexto do curso de Tecnologia em Sistemas para Internet da FIAP.*
