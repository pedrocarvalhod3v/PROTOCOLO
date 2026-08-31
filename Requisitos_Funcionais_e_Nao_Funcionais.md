# PROTOCOLO — Especificação de Requisitos de Software (RF e RNF)

---

## 1. Visão Geral dos Requisitos

Este documento formaliza os **Requisitos Funcionais (RF)** e **Requisitos Não Funcionais (RNF)** do sistema **PROTOCOLO**, projetado para automatizar a captação, triagem, gestão de prazos, habilitação documental (com foco em CAT) e apoio à análise orçamentária para construtoras de obras públicas de pequeno e médio porte.

> **Estratégia de Plataforma:**
> - **MVP (Fase 1):** Aplicação **Web Responsiva (Core do Produto)** + **Progressive Web App (PWA)** com notificações locais/web push.
> - **Fase 2 (Evolução):** Aplicativo **Mobile Nativo** (iOS/Android), viabilizado assim que o volume de usuários ativos justificar a publicação nas lojas e infraestrutura dedicada de push notification avançada.

---

## 2. Requisitos Funcionais (RF)

---

### Módulo 1: Agregação e Ingestão de Editais Multi-Portal

#### RF-01 — Ingestão Automática de Editais do PNCP
**Descrição:** "O sistema deverá integrar-se periodicamente à API pública do Portal Nacional de Contratações Públicas (PNCP) para capturar, importar e atualizar novos editais, retificações e avisos de licitação publicados em âmbito nacional."

#### RF-02 — Coleta de Editais de Portais Estaduais e Municipais
**Descrição:** "O sistema deverá possuir conectores e rotinas de coleta automatizada (web scraping) para extrair editais e publicações de portais relevantes (ex.: BLL Compras, BNC, Portal de Compras Públicas e portais estaduais/municipais prioritários)."

#### RF-03 — Deduplicação e Unificação de Editais
**Descrição:** "O sistema deverá identificar e unificar registros de um mesmo edital que tenha sido publicado concomitantemente em múltiplos portais, consolidando-os em uma única oportunidade e vinculando todas as fontes de origem."

#### RF-04 — Visualização de Metadados e Link da Fonte Oficial
**Descrição:** "O sistema deverá exibir em cada edital os metadados consolidados (órgão licitante, número do edital, modalidade, objeto, data de abertura, valor estimado) juntamente com o link direto para a publicação no portal de origem."

---

### Módulo 2: Filtros e Motor de Busca Avançada

#### RF-05 — Filtro Parametrizado por Segmento e Tipo de Obra
**Descrição:** "O sistema deverá permitir filtrar editais por categorias específicas da construção civil e obras de pequeno/médio porte (ex.: edificações, reformas, escolas, hospitais, praças, parques, pavimentação e infraestrutura viária)."

#### RF-06 — Filtro Geográfico por Estado e Município
**Descrição:** "O sistema deverá disponibilizar filtros de localização geográfica, permitindo ao usuário selecionar um estado específico (ex.: Goiás), múltiplos estados ou raio de municípios de seu interesse."

#### RF-07 — Busca Textual no Objeto e Anexos do Edital
**Descrição:** "O sistema deverá permitir a busca por palavras-chave e termos técnicos no título, resumo do objeto e no texto dos editais indexados."

#### RF-08 — Criação e Salvamento de Perfis de Busca
**Descrição:** "O sistema deverá permitir que o usuário salve combinações de filtros (tipo de obra + região) como 'Perfis de Busca Favoritos' para acesso rápido no painel principal."

---

### Módulo 3: Painel de Priorização e Gestão de Oportunidades

#### RF-09 — Dashboard de Triagem e Oportunidades
**Descrição:** "O sistema deverá fornecer um painel visual (em formato de lista e quadro de status) que substitua as planilhas manuais, agrupando os editais capturados em fases de avaliação."

#### RF-10 — Atribuição de Nota de Prioridade e Decisão de Participação
**Descrição:** "O sistema deverá permitir que o usuário atribua uma nota de prioridade (ex.: escala de 1 a 5 ou Alta/Média/Baixa) e defina o status da oportunidade (Em Análise, Qualificado para Participar / Go, Descartado / No-Go)."

#### RF-11 — Registro de Observações e Anotações Internas por Edital
**Descrição:** "O sistema deverá disponibilizar um campo de anotações internas por edital para que engenheiros e orçamentistas registrem observações, pareceres e pendências sobre a oportunidade."

---

### Módulo 4: Gestão de Prazos e Alertas Cruzados

#### RF-12 — Linha do Tempo e Cronograma de Datas Críticas do Edital
**Descrição:** "O sistema deverá mapear e exibir a linha do tempo de cada licitação com todas as suas datas críticas: data limite para impugnação, data limite para esclarecimentos, data de abertura da sessão pública e prazo de envio de propostas."

#### RF-13 — Verificação Cruzada de Prazos entre Fontes
**Descrição:** "O sistema deverá confrontar os prazos capturados em diferentes portais para o mesmo edital, alertando o usuário em caso de divergência e indicando a data e hora da última checagem bem-sucedida em cada fonte."

#### RF-14 — Notificações de Alerta de Vencimento de Prazos
**Descrição:** "O sistema deverá disparar alertas visuais e notificações (via PWA Web Push e e-mail) com antecedência configurável (ex.: 48h, 24h e 2h antes) para prazos de envio de proposta, impugnação e abertura de sessão."

---

### Módulo 5: Módulo Protocolo — Habilitação Técnica (CAT) e Licenciamentos

#### RF-15 — Cadastro e Gestão de Certidões de Acervo Técnico (CAT)
**Descrição:** "O sistema deverá tratar a CAT (Certidão de Acervo Técnico) como entidade de primeira classe, permitindo cadastrar o atestado, engenheiro responsável, órgão emissor (CREA/CAU), tipo de obra executada, quantitativos e anexar o PDF comprobatório."

#### RF-16 — Cadastro de Licenças e Certidões de Habilitação
**Descrição:** "O sistema deverá permitir o cadastro de documentos habilitatórios da empresa (alvarás, certidões negativas municipais, estaduais e federais, licenças ambientais e sanitárias), com seus respectivos números, órgãos emissores e datas de validade."

#### RF-17 — Monitoramento e Alertas de Validade de Certidões e Licenças
**Descrição:** "O sistema deverá monitorar continuamente o prazo de validade das certidões e licenças cadastradas, gerando alertas no painel e por notificação quando um documento estiver próximo do vencimento (ex.: 30, 15 e 5 dias antes)."

#### RF-18 — Checklist de Habilitação Customizável por Edital
**Descrição:** "O sistema deverá permitir criar e personalizar um checklist documental específico para cada edital participante, mapeando as exigências peculiares de cada órgão e permitindo associar os documentos da empresa (CATs, certidões e declarações) a cada item exigido."

#### RF-19 — Geração de Pacote de Documentação para Submissão
**Descrição:** "O sistema deverá permitir a exportação/download consolidado dos documentos e certidões vinculados ao checklist do edital, facilitando a montagem rápida do dossiê de habilitação."

---

### Módulo 6: Painel de Apoio à Viabilidade Financeira (SINAPI / CO-INFRA e BDI)

#### RF-20 — Visualização Estruturada dos Itens da Planilha Orçamentária
**Descrição:** "O sistema deverá disponibilizar uma visualização dos itens da planilha de custos do edital (serviços, unidades de medida, quantidades e preços unitários estimados pelo órgão)."

#### RF-21 — Painel Comparativo com Tabelas de Referência (SINAPI / CO-INFRA)
**Descrição:** "O sistema deverá exibir lado a lado o preço unitário do item informado no edital e o preço de referência da tabela oficial vigente (SINAPI e/ou CO-INFRA), destacando desvios percentuais para subsidiar a análise crítica do engenheiro."

#### RF-22 — Painel Consultivo de Simulação de BDI
**Descrição:** "O sistema deverá permitir que o engenheiro informe o BDI (Benefícios e Despesas Indiretas) pretendido para a proposta e visualize o impacto comparativo nos preços finais dos itens frente ao teto estipulado pelo edital."

---

### Módulo 7: Gestão de Acessos e Usuários

#### RF-23 — Cadastro, Autenticação e Perfis de Acesso
**Descrição:** "O sistema deverá gerenciar usuários da construtora com autenticação segura (e-mail/senha com suporte a 2FA) e perfis de permissão diferenciados (ex.: Administrador/Diretor, Engenheiro Orçamentista, Analista de Licitação)."

---

## 3. Requisitos Não Funcionais (RNF)

---

### Categoria 1: Arquitetura, Plataforma e Portabilidade

#### RNF-01 — Interface Web Totalmente Responsiva (Core do Produto)
**Descrição:** "O sistema deverá possuir interface web projetada segundo a filosofia Mobile-First e Design Responsivo, adaptando-se sem perda funcional a telas desktop (monitores Full HD), tablets e smartphones (Android/iOS)."

#### RNF-02 — Suporte a Progressive Web App (PWA) no MVP
**Descrição:** "O sistema deverá implementar os padrões de PWA (Web App Manifest, Service Workers e Cache Estratégico), permitindo que o usuário instale o PROTOCOLO na tela inicial de seus dispositivos móveis e desktops sem necessidade de loja de aplicativos."

#### RNF-03 — Notificações Web Push via Service Worker
**Descrição:** "O sistema deverá implementar notificações push nativas de navegador via Service Worker no PWA, permitindo o recebimento de alertas de prazos mesmo com a aplicação fechada nos navegadores compatíveis."

#### RNF-04 — Arquitetura Preparada para Aplicativo Mobile Nativo (Fase 2)
**Descrição:** "O sistema deverá disponibilizar todas as funcionalidades de negócio através de uma API RESTful desacoplada (Backend-For-Frontend ou REST API pura com autenticação via JWT/OAuth2), garantindo que o futuro aplicativo mobile nativo (Fase 2) possa consumir os mesmos serviços sem necessidade de refatoração do backend."

---

### Categoria 2: Desempenho e Eficiência

#### RNF-05 — Tempo de Resposta da Interface de Usuário
**Descrição:** "O sistema deverá responder a ações de navegação, aplicação de filtros e consultas de editais no painel com tempo de resposta inferior a 2,0 segundos sob condições normais de conexão de internet."

#### RNF-06 — Processamento Assíncrono e Resiliente de Ingestão
**Descrição:** "O sistema deverá processar tarefas pesadas de ingestão de dados (chamadas à API do PNCP, web scraping e comparação de tabelas volumosas) em segundo plano através de filas assíncronas de trabalho, evitando travamento ou lentidão na API principal."

#### RNF-07 — Otimização de Armazenamento e Caching
**Descrição:** "O sistema deverá utilizar mecanismos de cache (ex.: Redis) para consultas repetidas a tabelas de referência estáticas (SINAPI e CO-INFRA) e índices de busca rápida, reduzindo a carga no banco de dados principal."

---

### Categoria 3: Confiabilidade, Disponibilidade e Integridade de Dados

#### RNF-08 — Alta Disponibilidade do Serviço
**Descrição:** "O sistema deverá operar com nível de disponibilidade mínima de 99,5% durante os dias úteis em horário comercial (das 07h às 19h), assegurando que o usuário não seja impedido de checar prazos no dia da sessão pública."

#### RNF-09 — Tolerância a Falhas na Coleta de Portais Externos
**Descrição:** "O sistema deverá implementar políticas de tolerância a falhas (retentativas com recuo exponencial e circuit breaker) para que a indisponibilidade momentânea de um portal externo de licitações não interrompa a coleta dos demais portais nem comprometa a integridade dos dados já armazenados."

#### RNF-10 — Rastreabilidade e Transparência da Origem dos Dados
**Descrição:** "O sistema deverá registrar e exibir explicitamente a data, a hora e a URL de origem de cada sincronização de edital, permitindo ao usuário saber exatamente o grau de atualização daquela informação."

---

### Categoria 4: Segurança e Conformidade Legal

#### RNF-11 — Criptografia de Dados em Trânsito e em Repouso
**Descrição:** "O sistema deverá garantir a comunicação exclusivamente através do protocolo seguro HTTPS com TLS 1.3 (ou superior) e criptografar em repouso as senhas (hashing com bcrypt/Argon2) e documentos sigilosos da empresa (AES-256)."

#### RNF-12 — Conformidade com a LGPD (Lei Geral de Proteção de Dados)
**Descrição:** "O sistema deverá estar em conformidade com a LGPD (Lei nº 13.709/2018), garantindo o consentimento explícito, isolamento de dados de cada construtora cliente (arquitetura multi-tenant lógica) e anonimização de logs de acesso."

#### RNF-13 — Backup Automatizado e Recuperação de Desastres
**Descrição:** "O sistema deverá executar rotinas de backup diário automatizado do banco de dados e arquivos de documentos (CATs e certidões), com tempo máximo de recuperação (RTO) de até 4 horas e ponto de recuperação (RPO) de no máximo 24 horas."

---

### Categoria 5: Usabilidade e Acessibilidade

#### RNF-14 — Facilidade de Uso e Eficiência de Navegação (UX)
**Descrição:** "O sistema deverá apresentar interface intuitiva orientada ao fluxo de trabalho de engenharia, permitindo que um usuário realize a triagem diária completa de novos editais em menos de 15 minutos (reduzindo drasticamente as 2 horas do processo manual anterior)."

#### RNF-15 — Acessibilidade e Boas Práticas de UI
**Descrição:** "O sistema deverá seguir as diretrizes da WCAG 2.1 nível AA para contraste visual de cores, legibilidade de tipografia técnica e compatibilidade com leitores de tela em elementos fundamentais de navegação."

---

### Categoria 6: Manutenibilidade e Padrões de Engenharia

#### RNF-16 — Documentação e Versionamento de APIs (OpenAPI / Swagger)
**Descrição:** "O sistema deverá manter toda a sua interface de serviços de backend documentada no padrão OpenAPI 3.0 (Swagger interativo), com versionamento semântico nas rotas (ex.: `/api/v1/editais`)."

#### RNF-17 — Cobertura e Automação de Testes de Software
**Descrição:** "O sistema deverá manter cobertura mínima de 70% de testes automatizados (unitários e de integração) nos módulos críticos de negócio (cálculo de prazos, regras de validade de CAT e algoritmos de deduplicação) executados automaticamente no pipeline de CI/CD."

---

## 4. Matriz Resumo: Requisitos x Fases do Projeto

| Requisito | Nome Resumido | Escopo MVP (Fase 1) | Fase 2 (Evolução Futura) |
| :--- | :--- | :---: | :---: |
| **RF-01 a RF-04** | Ingestão Multi-Portal (PNCP + Portais) | **Sim** | Expansão de Conectores |
| **RF-05 a RF-08** | Filtros e Perfis de Busca | **Sim** | Recomendações por IA |
| **RF-09 a RF-11** | Painel de Priorização (Kanban/Lista) | **Sim** | Histórico Avançado |
| **RF-12 a RF-14** | Linha do Tempo e Alertas Cruzados | **Sim** | Alertas via WhatsApp/SMS |
| **RF-15 a RF-19** | Módulo Protocolo (CAT, Licenças e Checklist) | **Sim** | OCR Automático de CATs |
| **RF-20 a RF-22** | Painel Consultivo SINAPI/CO-INFRA e BDI | **Sim** | Heurística de Fraude em Tabelas |
| **RF-23** | Autenticação e Perfis de Acesso | **Sim** | SSO Corporativo |
| **RNF-01 a RNF-03**| Web Responsiva + PWA com Push | **Sim** | Suporte Offline Pleno |
| **RNF-04** | Aplicativo Mobile Nativo | Arquitetura Pronta | **App Nativo (iOS/Android)** |
| **RNF-05 a RNF-17**| Desempenho, Segurança, LGPD e CI/CD | **Sim** | Escala Multi-Região |
