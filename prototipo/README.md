# PROTOCOLO — Protótipo Funcional Interativo (MVP)

Este diretório contém a implementação do **Protótipo Funcional Interativo** da plataforma **PROTOCOLO**, desenvolvido com base na pesquisa com usuário real do setor de construção civil e nas especificações de requisitos (RF-01 a RF-23).

---

## 1. Como Executar o Protótipo

O protótipo foi construído como uma aplicação web moderna (HTML5, Tailwind CSS e Vue.js 3 Reativo) e é **100% autossuficiente**, não necessitando de `npm install` ou compilação prévia:

### Opção A: Abertura Direta
- Basta dar um **duplo clique** no arquivo [`index.html`](file:///c:/Users/pedro.carvalho/Documents/PROTOCOLO-main/prototipo/index.html) para abri-lo em qualquer navegador moderno (Google Chrome, Microsoft Edge, Firefox).

### Opção B: Script de Inicialização Rápida
- Dê um duplo clique no script [`iniciar_prototipo.bat`](file:///c:/Users/pedro.carvalho/Documents/PROTOCOLO-main/prototipo/iniciar_prototipo.bat) para abrir a aplicação automaticamente no seu navegador padrão.

### Opção C: Servidor Local via Python (Opcional)
Se preferir rodar como um servidor HTTP local:
```bash
cd c:\Users\pedro.carvalho\Documents\PROTOCOLO-main\prototipo
python -m http.server 3000
```
E acesse `http://localhost:3000` no navegador.

---

## 2. Módulos e Funcionalidades Demonstradas

### 🎯 1. Triagem & Priorização de Editais (RF-05 a RF-11)
- **Alternador de Visualização:** Quadro Kanban (fases: *Descoberto*, *Em Análise*, *Qualificado Go*, *Descartado No-Go*) e Tabela Detalhada.
- **Filtros Especializados de Construção Civil:** Filtragem por segmento de obra (Pavimentação, Reformas, Edificações, Praças) e municípios do Estado de Goiás (Goiânia, Rio Verde, Anápolis).
- **Interatividade:**
  - Classificação por estrelas de prioridade (1 a 5).
  - Transição de status (ex: mover para *Go* ou *No-Go* com registro de motivo).
  - Exibição de fontes consolidadas (PNCP, BLL Compras, BNC, Compras Públicas).

### ⏱️ 2. Linha do Tempo & Verificação Cruzada de Prazos (RF-12 a RF-14)
- **Detecção Real de Divergência:** Demonstração do caso prático no Edital 042/2026 (Rio Verde), onde o PNCP indicava abertura às 09:00 e o portal municipal retificou para as 14:00.
- **SLA e Histórico de Sincronização:** Exibição da última checagem bem-sucedida em cada fonte.
- **Linha do Tempo Visual:** Alertas regressivos de 24h para impugnações e contagem de dias para sessões públicas.

### 📜 3. Módulo Protocolo — Acervo Técnico (CAT) & Dossiês (RF-15 a RF-19)
- **CAT como Entidade de 1ª Classe:** Lista de acervos técnicos da construtora com quantitativos executados (m² de pavimentação, concreto armado, etc.), engenheiro responsável e CREA-GO.
- **Modal de Cadastro de Nova CAT:** Permite cadastrar e indexar novo atestado técnico.
- **Painel de Licenças & Certidões:** Alertas automáticos por faixa de vencimento (Urgente $\le$ 5 dias, Atenção $\le$ 30 dias e Válida).
- **Checklist Customizável por Edital:** Vinculação automática entre as exigências do edital e as CATs/certidões da empresa (exibindo percentual de atendimento de quantitativo, ex: 225%).
- **Gerador de Dossiê de Submissão:** Consolidação e geração simulada do pacote `.zip` com todos os documentos indexados.

### 📊 4. Apoio à Viabilidade Financeira — SINAPI & Simulador BDI (RF-20 a RF-22)
- **Comparativo Lado a Lado:** Preços unitários da planilha do edital confrontados com a tabela oficial **SINAPI Goiás** e **CO-INFRA**.
- **Detecção de Distorções:** Destaque em cores para itens com margem folgada ($>+5\%$) ou sob suspeita de inexequibilidade ($<-5\%$, ex: alvenaria 9.5% abaixo da tabela).
- **Simulador Interativo de BDI:** Slider dinâmico para simular taxas de BDI (15% a 32%), calculando em tempo real o preço proposto final, valor em Reais do BDI e validação frente ao preço teto do edital.
