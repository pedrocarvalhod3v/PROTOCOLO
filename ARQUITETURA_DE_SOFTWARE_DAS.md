# PROTOCOLO — Documento de Arquitetura de Software (DAS)
**Versão:** 1.0  
**Data:** Setembro de 2026  
**Responsável Técnico:** Pedro (Tech Lead & Arquiteto de Software — M2)  
**Projeto:** PROTOCOLO — Plataforma de Monitoramento de Licitações e Habilitação Técnica

---

## 1. Visão Geral e Objetivos do Sistema

O **PROTOCOLO** é uma plataforma concebida para sanar o gargalo operacional enfrentado por construtoras de pequeno e médio porte na participação em licitações públicas no Brasil (com ênfase inicial em obras civis, infraestrutura viária e reformas no Estado de Goiás e região Centro-Oeste).

### 1.1 Metas Arquiteturais Primárias
1. **Agregação e Ingestão Resiliente:** Coleta periódica de editais via API oficial do PNCP (Portal Nacional de Contratações Públicas) e rotinas automatizadas de web scraping para portais complementares (BLL Compras, BNC, Compras Públicas), tolerando falhas externas e controlando a defasagem temporal de dados (**RF-01 a RF-04, RNF-09**).
2. **Deduplicação de Oportunidades:** Identificação algorítmica de editais idênticos publicados simultaneamente em múltiplas plataformas (**RF-03**).
3. **Módulo Protocolo — CAT como Entidade de Primeira Classe:** Centralização de Certidões de Acervo Técnico (CAT), engenheiros responsáveis, atestados com quantitativos executados, validade de licenças/certidões negativas e geração dinâmica de dossiês de habilitação (**RF-15 a RF-19**).
4. **Verificação Cruzada de Prazos e Alertas:** Monitoramento de divergências entre fontes oficiais e emissão de alertas precoces para impugnações, esclarecimentos e sessões públicas (**RF-12 a RF-14**).
5. **Apoio à Viabilidade Financeira:** Painel comparativo entre a planilha orçamentária do edital e tabelas de referência governamentais (SINAPI e CO-INFRA), com simulação paramétrica de BDI (**RF-20 a RF-22**).
6. **Evolução Mobile-First:** MVP disponibilizado via Web Responsiva e Progressive Web App (PWA) com Web Push, desacoplado de modo que a API RESTful sirva futuramente ao aplicativo mobile nativo (Fase 2) sem refatoração (**RNF-01 a RNF-04**).

---

## 2. Visão Arquitetural C4 Model

A arquitetura do PROTOCOLO adota o padrão **Modular Monolith orientado a eventos assíncronos**, desacoplando o tráfego de usuários interativos do processamento pesado de ingestão de dados e parsing de editais.

### 2.1 C4 — Nível 1: Diagrama de Contexto do Sistema

O diagrama abaixo ilustra as personas que utilizam a plataforma, os limites do sistema **PROTOCOLO** e as entidades externas integradas:

```mermaid
flowchart TB
    %% Estilização padrão C4
    classDef person fill:#08427b,stroke:#052a4f,stroke-width:2px,color:#ffffff;
    classDef system fill:#1168bd,stroke:#0b4884,stroke-width:2px,color:#ffffff;
    classDef external fill:#4b5563,stroke:#374151,stroke-width:2px,color:#ffffff;
    classDef boundary fill:#f8fafc,stroke:#cbd5e1,stroke-width:1.5px,stroke-dasharray: 4 4,color:#1e293b;

    subgraph USUARIOS [" 👥 Personas da Construtora Cliente "]
        direction LR
        eng["👷 <b>Engenheiro / Orçamentista</b><br/><i>Analisa viabilidade (SINAPI), CATs e prazos</i>"]:::person
        analista["📋 <b>Analista de Licitações</b><br/><i>Triagem diária, Go/No-Go e checklist</i>"]:::person
        diretor["💼 <b>Diretor / Administrador</b><br/><i>Funil de oportunidades e custos</i>"]:::person
    end

    subgraph PROTOCOLO_BOUNDARY [" 🏛️ Limites da Plataforma PROTOCOLO "]
        direction TB
        protocolo["🎯 <b>Sistema PROTOCOLO</b><br/><i>[Software System]</i><br/>Plataforma centralizada de monitoramento multi-portal, verificação cruzada de prazos, gestão de acervo técnico (CAT) e viabilidade orçamentária."]:::system
    end

    subgraph EXTERNOS [" 🌐 Sistemas e Provedores Externos "]
        direction LR
        pncp["🏛️ <b>API Pública do PNCP</b><br/><i>Portal Nacional de Contratações</i>"]:::external
        portais["📑 <b>Portais Complementares</b><br/><i>BLL Compras, BNC, Portais Municipais</i>"]:::external
        sinapi["📊 <b>Bases Oficiais de Custos</b><br/><i>Tabelas SINAPI (CEF) e CO-INFRA</i>"]:::external
        notif["🔔 <b>Serviços de Notificação</b><br/><i>Web Push (PWA) e E-mail Transacional</i>"]:::external
    end

    %% Conexões de Usuários para o Sistema
    eng -->|Consulta SINAPI, simula BDI e vincula CATs| protocolo
    analista -->|Filtra editais, define Go/No-Go e emite dossiês| protocolo
    diretor -->|Visualiza dashboards e métricas de conversão| protocolo

    %% Conexões do Sistema para Sistemas Externos
    protocolo -->|Consome editais e retificações via REST| pncp
    protocolo -->|Coleta avisos e atas via Web Scraping| portais
    protocolo -->|Importa insumos e composições de referência| sinapi
    protocolo -->|Dispara alertas de prazos e vencimento de certidões| notif
```

---

### 2.2 C4 — Nível 2: Diagrama de Contêineres

O sistema é particionado em contêineres desacoplados, organizados em camadas hierárquicas claras (Apresentação, Roteamento, Serviços de Aplicação e Persistência/Filas). Esse layout em camadas garante legibilidade total dos fluxos e elimina sobreposição de conectores:

```mermaid
flowchart TB
    %% Definições visuais com paleta oficial C4
    classDef person fill:#08427b,stroke:#052a4f,stroke-width:2px,color:#ffffff;
    classDef frontend fill:#1168bd,stroke:#0b4884,stroke-width:2px,color:#ffffff;
    classDef gateway fill:#2b5b84,stroke:#1d3e5a,stroke-width:2px,color:#ffffff;
    classDef backend fill:#1168bd,stroke:#0b4884,stroke-width:2px,color:#ffffff;
    classDef worker fill:#438dd5,stroke:#2b5b84,stroke-width:2px,color:#ffffff;
    classDef storage fill:#1f618d,stroke:#154360,stroke-width:2px,color:#ffffff;
    classDef tier fill:#f8fafc,stroke:#94a3b8,stroke-width:1.5px,stroke-dasharray: 4 4,color:#0f172a;

    subgraph TIER_CLIENTE [" 1. Camada de Apresentação & Usuário "]
        direction LR
        usuario["👤 <b>Usuário da Construtora</b><br/><i>[Engenheiro, Analista, Diretor]</i>"]:::person
        spa["💻 <b>Single Page Application (PWA)</b><br/><i>[Container: React / Next.js + Tailwind CSS]</i><br/>Interface responsiva mobile-first com cache local e Web Push"]:::frontend
        usuario -->|Interação via navegador / PWA| spa
    end

    subgraph TIER_BORDA [" 2. Camada de Borda & Segurança "]
        direction TB
        api_gateway["🛡️ <b>API Gateway & Reverse Proxy</b><br/><i>[Container: Nginx / Traefik]</i><br/>Terminação TLS 1.3, Roteamento, Rate Limiting e CORS"]:::gateway
    end

    subgraph TIER_CORE [" 3. Camada de Aplicação e Processamento (Backend) "]
        direction LR
        backend_api["⚙️ <b>Backend Core API</b><br/><i>[Container: FastAPI / NestJS]</i><br/>Autenticação JWT, Regras de CAT, Checklists, Comparador SINAPI e BDI"]:::backend
        worker_ingestion["🔄 <b>Ingestion & Sync Worker</b><br/><i>[Container: Celery / Python Asyncio]</i><br/>Coleta PNCP, Scrapers (BLL/BNC), Deduplicação e Alertas de Prazos"]:::worker
    end

    subgraph TIER_DADOS [" 4. Camada de Dados, Mensageria & Armazenamento "]
        direction LR
        db_relacional[("🗄️ <b>Banco Relacional Principal</b><br/><i>[Container: PostgreSQL 16]</i><br/>Editais, CATs, Checklists, RLS Multi-tenant e JSONB")]:::storage
        cache_queue[("⚡ <b>Fila Assíncrona & Cache</b><br/><i>[Container: Redis 7]</i><br/>Filas Celery de scraping, cache de tabelas SINAPI e sessões")]:::storage
        object_storage[("📦 <b>Storage de Documentos</b><br/><i>[Container: MinIO / S3]</i><br/>PDFs de CATs, certidões e editais com criptografia AES-256")]:::storage
    end

    %% Fluxo de Requisições do Usuário (Top-Down Limpo)
    spa -->|Requisições RESTful / JSON / WSS| api_gateway
    api_gateway -->|Encaminha chamadas com validação JWT| backend_api

    %% Conexões do Backend Core API com a camada de dados
    backend_api -->|Consultas SQL e RLS multi-tenant| db_relacional
    backend_api -->|Cache de tabelas e despacho de jobs| cache_queue
    backend_api -->|Download e streaming de PDFs / Dossiês| object_storage

    %% Conexões dos Workers Assíncronos (Desacoplados via Redis)
    cache_queue -.->|1. Consome tarefas de coleta agendadas| worker_ingestion
    worker_ingestion -.->|2. Persiste editais e divergências| db_relacional
    worker_ingestion -.->|3. Armazena PDFs brutos coletados| object_storage
```

---

### 2.3 C4 — Nível 3: Diagrama de Componentes dos Módulos Principais

#### 2.3.1 Subsistema de Ingestão e Deduplicação (Responsável: Rafael — M3)
O pipeline garante o desacoplamento de cada conector de dados, isolando falhas e tratando editais idênticos publicados em múltiplos portais:

```mermaid
flowchart TD
    classDef trigger fill:#0284c7,stroke:#0369a1,stroke-width:2px,color:#ffffff;
    classDef collector fill:#438dd5,stroke:#2b5b84,stroke-width:2px,color:#ffffff;
    classDef engine fill:#1168bd,stroke:#0b4884,stroke-width:2px,color:#ffffff;
    classDef storage fill:#1f618d,stroke:#154360,stroke-width:2px,color:#ffffff;
    classDef decision fill:#eab308,stroke:#ca8a04,stroke-width:2px,color:#000000;

    subgraph SCHEDULER_TIER [" Agendamento & Disparo "]
        A["⏱️ <b>Scheduler Cron</b><br/>Disparo a cada 30 min"]:::trigger --> B["📋 <b>Task Dispatcher</b><br/>Enfileira jobs no Redis"]:::trigger
    end

    subgraph COLLECTORS_TIER [" Conectores de Fontes Oficiais "]
        direction LR
        C["🏛️ <b>PNCP Connector</b><br/>API Pública REST"]:::collector
        D["📑 <b>BLL Scraper</b><br/>Rotinas Web Scraping"]:::collector
        E["🔍 <b>BNC / Portais Municipais</b><br/>Conectores Customizados"]:::collector
    end

    subgraph PROCESSING_TIER [" Pipeline de Saneamento & Deduplicação "]
        direction TB
        F["⚙️ <b>Normalizer Engine</b><br/>Padronização de formatos e datas"]:::engine
        G["🔬 <b>Deduplication Matcher</b><br/>Cálculo de similaridade e hash do objeto"]:::engine
        H{"Edital Já<br/>Cadastrado?"}:::decision
        I["🔗 <b>Link de Fonte Adicional</b><br/>Registra SLA e checa divergência de prazos"]:::engine
        J["✨ <b>Cadastra Novo Edital</b><br/>Indexa e agenda alertas de prazos"]:::engine
    end

    subgraph STORAGE_TIER [" Persistência "]
        K[("🗄️ <b>PostgreSQL 16</b><br/>Tabelas: Edital, FontePublicacao, AlertaPrazo")]:::storage
    end

    B --> C
    B --> D
    B --> E

    C -->|Raw JSON| F
    D -->|Raw HTML| F
    E -->|Raw HTML| F

    F --> G
    G --> H

    H -->|Sim: Mesmo Edital| I
    H -->|Não: Nova Oportunidade| J

    I --> K
    J --> K
```

#### 2.3.2 Subsistema de Domínio e Módulo Protocolo (Responsável: Matheus — M4)
Centraliza a regra de negócio da CAT como entidade de primeira classe e a conformidade documental:

```mermaid
flowchart TD
    classDef controller fill:#0284c7,stroke:#0369a1,stroke-width:2px,color:#ffffff;
    classDef service fill:#1168bd,stroke:#0b4884,stroke-width:2px,color:#ffffff;
    classDef entity fill:#1f618d,stroke:#154360,stroke-width:2px,color:#ffffff;

    subgraph CONTROLLER_LAYER [" Entrada de API "]
        L["🚪 <b>API Controller: /api/v1/protocolo</b><br/>Validação de esquema e autorização"]:::controller
    end

    subgraph DOMAIN_SERVICES [" Serviços de Domínio "]
        direction LR
        M["📜 <b>CAT Service</b><br/>Atestados, CREA e quantitativos"]:::service
        N["⏰ <b>Certidão Monitor</b><br/>Validade (30d, 15d, 5d)"]:::service
        O["✅ <b>Checklist Builder</b><br/>Conformidade documental por edital"]:::service
        P["📊 <b>SINAPI / BDI Engine</b><br/>Comparador orçamentário"]:::service
    end

    subgraph ENTITY_LAYER [" Modelos de Domínio Persistidos "]
        direction LR
        Q[("📋 <b>Entidade CAT</b><br/>Acervos Técnicos")]:::entity
        R[("📑 <b>Entidade Certidão</b><br/>Licenças & CNDs")]:::entity
        S[("🗂️ <b>Checklist do Edital</b><br/>Dossiê Vinculado")]:::entity
        T[("📈 <b>Tabela Referência</b><br/>SINAPI e CO-INFRA")]:::entity
    end

    L --> M
    L --> N
    L --> O
    L --> P

    M -->|Gerencia acervos e ARTs| Q
    N -->|Checa prazos de expiração| R
    O -->|Mapeia exigências e associa documentos| S
    P -->|Cruza preços unitários e calcula BDI| T
```

---

## 3. Registros de Decisões Arquiteturais (ADRs)

### ADR-01: Modelo de Armazenamento Híbrido (PostgreSQL Relacional + JSONB + MinIO)
- **Status:** Aprovado.
- **Contexto:** Editais de órgãos municipais possuem estruturas extremamente variáveis (anexos em formatos diversos, tabelas sem padrão e campos personalizados), enquanto o relacionamento entre Construtora, CATs, Engenheiros e Checklists é altamente relacional e sensível a integridade referencial.
- **Decisão:** Adotar **PostgreSQL 16** como banco principal, usando tabelas relacionais fortemente tipadas para o domínio (`Empresa`, `Usuario`, `CAT`, `Certidao`, `ChecklistEdital`) e colunas `JSONB` indexadas via GIN para os atributos dinâmicos do edital (`metadados_especificos`, `campos_orgao`). Os arquivos físicos de editais e comprovantes de CAT são guardados no **MinIO/S3**.
- **Consequências:** Integridade transacional garantida onde mais importa, sem perder a agilidade de absorver formatos imprevisíveis de novos portais de licitação.

### ADR-02: Mensageria Assíncrona e Resiliência de Scraping (Redis + Celery + Circuit Breaker)
- **Status:** Aprovado.
- **Contexto:** A coleta de portais públicos e privados frequentemente enfrenta quedas, lentidões de até 40 segundos e bloqueios de IP. O usuário não pode ter sua navegação travada por essas oscilações.
- **Decisão:** Todas as coletas rodam de forma assíncrona desacopladas da API pública via **Redis e Celery Workers**. O conector de cada portal implementa o padrão **Circuit Breaker** (interrompe temporariamente requisições se houver 5 falhas consecutivas) e **Exponential Backoff** com jitter.
- **Consequências:** A API responde em menos de 200ms para o usuário final, e cada portal tem seu log de defasagem registrado individualmente (`ultima_checagem_sucesso`).

### ADR-03: Multi-Tenancy Lógico com RLS (Row Level Security) e Isolamento LGPD
- **Status:** Aprovado.
- **Contexto:** Informações sobre quais licitações uma construtora está participando, suas anotações estratégicas e seus acervos técnicos (CATs) são informações comerciais altamente sigilosas.
- **Decisão:** Implementação de arquitetura multi-tenant lógica onde toda consulta e gravação inclui obrigatoriamente o `tenant_id` (ID da Construtora), reforçado por **Row Level Security (RLS)** nativo do PostgreSQL e autenticação stateless via tokens JWT assinados com chave assimétrica (RS256).
- **Consequências:** Risco zero de vazamento cruzado de dados entre construtoras concorrentes, em total conformidade com a LGPD e RNF-12.

---

## 4. Diagrama Entidade-Relacionamento (DER)

O modelo de dados posiciona a **CAT (Certidão de Acervo Técnico)** como entidade de primeira classe de negócio:

```mermaid
erDiagram
    EMPRESA ||--o{ USUARIO : possui
    EMPRESA ||--o{ CAT : possui
    EMPRESA ||--o{ CERTIDAO : mantem
    EMPRESA ||--o{ PERFIL_BUSCA : configura
    EMPRESA ||--o{ LICITACAO_FAVORITA : monitora

    ENGENHEIRO ||--o{ CAT : responsavel_tecnico

    EDITAL ||--|{ FONTE_PUBLICACAO : publicada_em
    EDITAL ||--o{ ALERTA_PRAZO : gera
    EDITAL ||--o{ ITEM_ORCAMENTARIO : contem
    EDITAL ||--o{ LICITACAO_FAVORITA : referenciada_por

    LICITACAO_FAVORITA ||--o{ CHECKLIST_EDITAL : gera
    CHECKLIST_EDITAL ||--o{ CHECKLIST_ITEM : compoe
    CHECKLIST_ITEM }o--o| CAT : atende_com
    CHECKLIST_ITEM }o--o| CERTIDAO : comprova_com

    ITEM_ORCAMENTARIO }o--o| TABELA_REFERENCIA_ITEM : compara_com

    EMPRESA {
        uuid id PK
        string razao_social
        string cnpj UK
        string email_corporativo
        string plano
        timestamp criado_em
    }

    ENGENHEIRO {
        uuid id PK
        uuid empresa_id FK
        string nome_completo
        string registro_crea_cau
        string formacao_especialidade
    }

    CAT {
        uuid id PK
        uuid empresa_id FK
        uuid engenheiro_id FK
        string numero_cat
        string orgao_emissor
        string objeto_obra
        string categoria_obra
        decimal volume_quantitativo_principal
        string unidade_medida
        string anexo_pdf_url
        date data_emissao
        string status_validacao
    }

    CERTIDAO {
        uuid id PK
        uuid empresa_id FK
        string tipo_documento
        string orgao_expedidor
        string numero_registro
        date data_emissao
        date data_vencimento
        string arquivo_url
        string status_validade
    }

    EDITAL {
        uuid id PK
        string hash_deduplicacao UK
        string orgao_licitante
        string numero_edital
        string modalidade
        text objeto_resumo
        string segmento_obra
        string estado
        string municipio
        decimal valor_estimado
        timestamp data_abertura_sessao
        timestamp data_limite_impugnacao
        timestamp data_limite_proposta
        string status_ciclo_vida
    }

    FONTE_PUBLICACAO {
        uuid id PK
        uuid edital_id FK
        string nome_portal
        string url_publicacao
        timestamp data_captura
        timestamp ultima_checagem_sucesso
        boolean divergencia_detectada
    }

    ALERTA_PRAZO {
        uuid id PK
        uuid edital_id FK
        string tipo_alerta
        timestamp data_disparo_prevista
        boolean disparado
        text mensagem
    }

    LICITACAO_FAVORITA {
        uuid id PK
        uuid empresa_id FK
        uuid edital_id FK
        int nota_prioridade
        string decisao_participacao
        text anotacoes_internas
        decimal bdi_simulado_percentual
    }

    CHECKLIST_ITEM {
        uuid id PK
        uuid checklist_edital_id FK
        string exigencia_edital
        string tipo_exigido
        uuid cat_vinculada_id FK
        uuid certidao_vinculada_id FK
        boolean aprovado_interno
    }

    ITEM_ORCAMENTARIO {
        uuid id PK
        uuid edital_id FK
        string codigo_servico
        string descricao_servico
        string unidade
        decimal quantidade
        decimal preco_unitario_edital
        decimal preco_total_edital
    }

    TABELA_REFERENCIA_ITEM {
        uuid id PK
        string tabela_origem
        string codigo_composicao
        string descricao
        decimal preco_desonerado
        decimal preco_nao_desonerado
        date mes_ano_referencia
        string uf
    }
```

---

## 5. Máquinas de Estados Centrais

### 5.1 Ciclo de Vida da Oportunidade / Edital
```mermaid
stateDiagram-v2
    [*] --> Descoberto: Ingestão Automática (PNCP / Scraping)
    Descoberto --> Em_Analise: Triagem pelo Orçamentista
    Em_Analise --> Descartado_NoGo: Não Atende Requisitos / Inviável
    Em_Analise --> Qualificado_Go: Aprovado para Participar
    Qualificado_Go --> Documentacao_Em_Montagem: Checklist Iniciado
    Documentacao_Em_Montagem --> Submetido: Dossiê Completo Enviado
    Submetido --> Homologado_Ganho: Vitória / Adjudicado
    Submetido --> Perdido_Recurso: Derrota / Em Recurso
```

### 5.2 Ciclo de Monitoramento de Validade da CAT e Certidões
```mermaid
stateDiagram-v2
    [*] --> Valida: Cadastrada com Comprovante
    Valida --> Alerta_30_Dias: Faltam <= 30 dias para expirar
    Alerta_30_Dias --> Alerta_Critico_5_Dias: Faltam <= 5 dias para expirar
    Alerta_Critico_5_Dias --> Expirada: Data Vencida
    Expirada --> Renovada_Valida: Novo PDF / Nova Emissão
    Renovada_Valida --> Valida
```

---

## 6. Estratégia de Segurança, Criptografia e LGPD

- **TLS 1.3 Obrigatório:** Toda comunicação externa e de PWA utiliza exclusivamente cifras seguras modernas.
- **Criptografia em Repouso:** Os documentos no Object Storage (MinIO) utilizam criptografia server-side via SSE-S3 / AES-256. Senhas são protegidas com `Argon2id`.
- **Rastreabilidade e Log de Auditoria:** Qualquer ação de alteração de nota de prioridade, exclusão de certidão ou inclusão de anotação gera evento imutável com data, hora, IP e usuário responsável.
- **Conformidade LGPD:** Os dados pessoais de engenheiros técnicos (CREA, CPF, e-mails de contato) são categorizados com flags de privacidade e podem ser anonimizados mediante solicitação expressa do titular.

---

## 7. Estratégia de Implantação e DevOps

- **Contêineres de Desenvolvimento:** `docker-compose.yml` com serviços pré-configurados: `api`, `frontend`, `db`, `redis`, `worker` e `minio`.
- **Pipeline de Integração Contínua (CI/CD):**
  1. *Linting & Typecheck:* Ruff / ESLint + TypeScript strict mode.
  2. *Pirâmide de Testes:* Testes unitários com pytest/Jest para validação de prazos, cálculo de BDI e deduplicação de editais. Cobertura mínima de 70% (**RNF-17**).
  3. *Security Scan:* Trivy e pip-audit em containers Docker.
  4. *Deploy Automatizado:* Build de imagens e publicação em ambiente staging/produção.
