# Padrões Arquiteturais - IA Collab OS

**Padrões descobertos em projetos reais usando o framework**

Este documento cataloga padrões arquiteturais reutilizáveis identificados durante o desenvolvimento de projetos com o framework IA Collab OS. Cada padrão foi validado em contexto real e pode ser aplicado a outros projetos.

---

## 📋 Índice de Padrões

1. [Metadata-Driven Architecture](#1-metadata-driven-architecture)
2. [Multiple Orchestrators by Responsibility](#2-multiple-orchestrators-by-responsibility)
3. [Orchestrator-Specialist Pattern](#3-orchestrator-specialist-pattern-webhooks)
4. [Database Views for Business Logic](#4-database-views-for-business-logic)
5. [Workflow Naming Conventions](#5-workflow-naming-conventions)

---

## 1. Metadata-Driven Architecture

### Quando Usar

Use este padrão quando:
- ✅ Sistema precisa ser **configurável sem deploys**
- ✅ Adicionar funcionalidades novas deve ser **trivial** (minutos, não horas)
- ✅ Há **escalabilidade horizontal** (muitas variações do mesmo padrão)
- ✅ Configurações mudam **frequentemente**

Evite se:
- ❌ Configurações são extremamente estáveis
- ❌ Número de variações é fixo e pequeno (<5)
- ❌ Lógica é muito complexa e específica

### Descrição

Em vez de hardcoding configurações no código, centralize-as em uma **tabela de metadados**. O código lê essas configurações em runtime e ajusta seu comportamento dinamicamente.

### Implementação

**Passo a passo:**

1. **Identifique o que varia**
   - Exemplos: indicadores, fontes de dados, regras de negócio, workflows, integrações

2. **Design da tabela de metadados**
   ```sql
   CREATE TABLE config_metadata (
     id UUID PRIMARY KEY,
     code VARCHAR UNIQUE NOT NULL,  -- Identificador único
     name VARCHAR NOT NULL,          -- Nome legível
     config_data JSONB,              -- Configurações específicas
     is_active BOOLEAN DEFAULT true,
     created_at TIMESTAMPTZ DEFAULT NOW(),
     updated_at TIMESTAMPTZ DEFAULT NOW()
   );
   ```

3. **Código lê metadados em runtime**
   ```javascript
   // Em vez de:
   const indicators = ['PIB', 'POPULAÇÃO'];  // Hardcoded

   // Faça:
   const indicators = await db.query(
     'SELECT code, config_data FROM config_metadata WHERE is_active = true'
   );
   ```

4. **Adicionar novo item = INSERT**
   ```sql
   INSERT INTO config_metadata (code, name, config_data)
   VALUES ('NOVO_ITEM', 'Nome do Item', '{"param": "valor"}'::jsonb);
   ```

### Exemplo Real: Tocantins Integrado

**Contexto**: Sistema de coleta de indicadores municipais

**Antes** (hardcoded):
```javascript
// Workflow n8n com 2 indicadores fixos
const indicators = [
  {
    code: 'PIB_TOTAL',
    url: 'https://api.ibge.gov.br/...'
  },
  {
    code: 'POPULACAO',
    url: 'https://api.ibge.gov.br/...'
  }
];
// Adicionar novo indicador = editar código do workflow
```

**Depois** (metadata-driven):
```sql
-- Tabela indicator_dictionary
CREATE TABLE indicator_dictionary (
  id UUID PRIMARY KEY,
  code VARCHAR(100) UNIQUE,
  name VARCHAR(300),
  dimension VARCHAR(50),
  source_name VARCHAR(200),
  api_endpoint TEXT,           -- URL com placeholders
  api_params JSONB,
  periodicity VARCHAR(50),
  is_active BOOLEAN
);

-- Workflow lê metadados
SELECT code, api_endpoint, api_params
FROM indicator_dictionary
WHERE is_active = true;

-- Construção dinâmica de URL
url = api_endpoint.replace('{ibge_code}', municipality.ibge_code);
```

**Adicionar novo indicador**:
```sql
-- 1 INSERT (0 linhas de código)
INSERT INTO indicator_dictionary VALUES (
  gen_random_uuid(),
  'ECON_IDEB',
  'IDEB - Índice de Desenvolvimento da Educação Básica',
  'SOCIAL',
  'INEP',
  'https://api.inep.gov.br/ideb/{municipality_code}',
  '{"year": "2023"}'::jsonb,
  'annual',
  true
);
```

**Resultado**:
- 📈 **Escalabilidade**: De 2 para 55+ indicadores sem tocar código
- ⏱️ **Velocidade**: Adicionar indicador: 2 minutos (antes: 1-2 horas)
- 🔒 **Segurança**: Mudanças não exigem deploy (reduz risco)

### Trade-offs

| Vantagem | Desvantagem |
|----------|-------------|
| ✅ Escalabilidade extrema | ❌ Complexidade inicial maior |
| ✅ Configuração sem deploy | ❌ Debug menos óbvio |
| ✅ Mudanças não-técnicas podem configurar | ❌ Requer design cuidadoso da tabela |
| ✅ Auditoria (histórico em banco) | ❌ Performance (mais queries) |

### Quando o Padrão Falhou

- **Lógica muito complexa**: Regras de negócio com 20+ condições não cabem bem em JSONB
- **Performance crítica**: Queries adicionais podem impactar latência em sistemas de alta frequência
- **Debugging**: Erros em metadados são mais difíceis de rastrear que erros em código

---

## 2. Multiple Orchestrators by Responsibility

### Quando Usar

Use este padrão quando:
- ⚠️ Orquestrador único tem **>15 nós**
- ⚠️ Workflow mistura **responsabilidades distintas**
- ⚠️ **Schedules diferentes** (sob demanda vs cron)
- ⚠️ **Públicos diferentes** (usuários finais vs processos internos)

### Sinais de Alerta

Você precisa separar orquestradores quando:
- 🚨 "Este workflow faz análise **E** coleta **E** notificações..."
- 🚨 Workflow difícil de entender ou manter
- 🚨 Mudanças em uma parte quebram outra parte
- 🚨 Time discute "por que isso está neste workflow?"

### Descrição

Em vez de um orquestrador monolítico, crie **múltiplos orquestradores especializados**, cada um com uma responsabilidade clara.

### Implementação

**Critérios de separação:**

1. **Por função**: Análise vs Coleta vs Notificação
2. **Por schedule**: Tempo real (webhook) vs Batch (cron)
3. **Por domínio**: Vendas vs Financeiro vs Operações
4. **Por SLA**: Crítico (<100ms) vs Normal (<5s) vs Background (assíncrono)

**Padrão de nomenclatura:**
```
{funcao}-orchestrator.json
```

### Exemplo Real: Tocantins Integrado

**Antes** (orquestrador monolítico):
```
orchestrator.json
├─ Classificar consulta (análise)
├─ Chamar agentes (análise)
├─ Consolidar respostas (análise)
├─ Verificar indicadores pendentes (coleta)
├─ Chamar APIs externas (coleta)
└─ Atualizar banco (coleta)

16 nós | 2 responsabilidades | Difícil manter
```

**Depois** (orquestradores separados):
```
analysis-orchestrator.json
├─ Trigger: Webhook (sob demanda)
├─ Classificar consulta
├─ Chamar agentes ECON/SOCIAL/TERRA/AMBIENT
└─ Consolidar respostas
8 nós | 1 responsabilidade | Clara

data-collection-orchestrator.json
├─ Trigger: Cron (diário às 3h)
├─ Consultar indicadores pendentes
├─ Agrupar por fonte
├─ Chamar workflows especialistas
└─ Consolidar estatísticas
7 nós | 1 responsabilidade | Clara
```

**Benefícios obtidos**:
- ✅ **Clareza**: Cada orquestrador tem propósito óbvio
- ✅ **Manutenibilidade**: Mudanças isoladas (coleta não afeta análise)
- ✅ **Schedules independentes**: Análise sob demanda, coleta diária
- ✅ **Falhas isoladas**: Bug na coleta não quebra análises
- ✅ **Testes mais fáceis**: Cada orquestrador testável isoladamente

### Decisão via ADR

Documente a separação em ADR:

```markdown
# ADR-XXX: Separar Orquestradores por Responsabilidade

## Contexto
Orquestrador único com 16 nós está difícil de manter. Mistura análise
em tempo real (webhook) com coleta batch (cron diário).

## Decisão
Separar em dois orquestradores:
- analysis-orchestrator.json (análise sob demanda)
- data-collection-orchestrator.json (coleta diária)

## Alternativas
1. Manter monolítico: Rejeitada (já difícil manter)
2. Separar por fonte: Rejeitada (não resolve schedule)
3. **Separar por responsabilidade: ESCOLHIDA**

## Consequências
+ Cada orquestrador < 10 nós (simples)
+ Schedules independentes
+ Falhas isoladas
- Mais arquivos para gerenciar (2 vs 1)
```

### Lição Principal

**Regra de ouro**: Quando orquestrador excede 15 nós OU mistura 2+ responsabilidades → considere separação.

---

## 3. Orchestrator-Specialist Pattern (Webhooks)

### Quando Usar

- Sistema com **múltiplas fontes/agentes** especializados
- **Coordenação centralizada** necessária
- Execuções **paralelas ou assíncronas**
- Necessidade de **isolamento de falhas**

### Descrição

Separe lógica de coordenação (orquestrador) da lógica de execução (especialistas). Orquestrador decide **o quê** fazer; especialistas decidem **como** fazer.

### Arquitetura

```
┌─────────────────────────────────────┐
│         ORQUESTRADOR                │
│  - Decide quem chamar               │
│  - Prepara payload                  │
│  - Consolida resultados             │
└──────┬──────┬──────┬─────────────┬──┘
       │      │      │             │
    Webhook Webhook Webhook     Webhook
       │      │      │             │
       ▼      ▼      ▼             ▼
   ┌────┐  ┌────┐ ┌────┐      ┌────┐
   │ E1 │  │ E2 │ │ E3 │ ...  │ EN │  Especialistas
   └────┘  └────┘ └────┘      └────┘
```

### Implementação

**1. Orquestrador prepara payload:**
```javascript
// Agrupa trabalho por especialista
const tasks = groupBySpecialist(items);

// Para cada especialista
for (const specialist of tasks) {
  await callWebhook({
    url: `${BASE_URL}/webhook/${specialist.name}`,
    payload: {
      orchestrator_run_id: runId,
      items: specialist.items,
      config: specialist.config
    }
  });
}
```

**2. Especialista executa:**
```javascript
// Recebe payload via webhook
const { orchestrator_run_id, items, config } = req.body;

// Executa lógica específica
const results = await processItems(items, config);

// Retorna resumo
return {
  run_id: orchestrator_run_id,
  specialist: 'IBGE',
  processed: results.success,
  errors: results.errors,
  duration_ms: elapsed
};
```

**3. Orquestrador consolida:**
```javascript
const summary = {
  total_specialists: specialists.length,
  total_items: sum(results.map(r => r.processed)),
  total_errors: sum(results.map(r => r.errors)),
  duration_ms: elapsed
};
```

### Exemplo Real: Coleta de Dados

**Orquestrador de Coleta:**
- Consulta: "Quais indicadores precisam ser coletados hoje?"
- Agrupa por fonte: IBGE (10 indicadores), INEP (5), MapBiomas (3)
- Chama 3 especialistas via webhook com lista de indicadores

**Especialista IBGE:**
- Recebe: 10 indicadores com metadados (api_endpoint, api_params)
- Executa: Loop por 139 municípios x 10 indicadores = 1.390 requests
- Retorna: 1.350 sucessos, 40 erros, 45 segundos

**Especialista INEP:**
- Recebe: 5 indicadores
- Executa: Lógica específica para API do INEP
- Retorna: Resumo

**Benefícios**:
- ✅ **Isolamento**: Erro em IBGE não quebra INEP
- ✅ **Paralelização**: Especialistas executam em paralelo
- ✅ **Desenvolvimento independente**: Times diferentes podem trabalhar em especialistas
- ✅ **Testabilidade**: Especialista testável isoladamente
- ✅ **Reutilização**: Especialista IBGE pode ser chamado por múltiplos orquestradores

### Trade-offs

| Vantagem | Desvantagem |
|----------|-------------|
| ✅ Isolamento de falhas | ❌ Complexidade de rede (webhooks) |
| ✅ Escalabilidade horizontal | ❌ Debugging distribuído mais difícil |
| ✅ Desenvolvimento paralelo | ❌ Latência adicional (HTTP) |
| ✅ Testabilidade | ❌ Gestão de estado distribuído |

---

## 4. Database Views for Business Logic

### Quando Usar

- Lógica complexa usada em **múltiplos lugares**
- Agregações/filtros que **mudam frequentemente**
- Necessidade de **testar lógica isoladamente** (sem código)
- **Performance** (views materializadas)

### Descrição

Em vez de duplicar lógica em código (API, workflows, relatórios), centralize em **database views**. Todos os consumers leem a mesma view.

### Implementação

**Exemplo: "Quais indicadores coletar hoje?"**

**Antes** (lógica em código):
```javascript
// Em 3 lugares diferentes: API, workflow, cron job
function getIndicatorsTODCollect() {
  return db.query(`
    SELECT * FROM indicators
    WHERE is_active = true
      AND next_collection_date <= CURRENT_DATE
  `);
}
// Lógica duplicada, difícil de manter
```

**Depois** (view centralizada):
```sql
-- Migration: 008_collection_views.sql
CREATE OR REPLACE VIEW v_indicators_pending_collection AS
SELECT
  id,
  code,
  name,
  dimension,
  source_name,
  api_endpoint,
  api_params,
  periodicity,
  last_ref_date,
  next_collection_date,
  CASE
    WHEN next_collection_date IS NULL THEN 'never_collected'
    WHEN next_collection_date < CURRENT_DATE THEN 'overdue'
    WHEN next_collection_date = CURRENT_DATE THEN 'due_today'
    ELSE 'future'
  END as collection_status
FROM indicator_dictionary
WHERE is_active = true
  AND collection_method IN ('api', 'scraping')
  AND (next_collection_date IS NULL
       OR next_collection_date <= CURRENT_DATE)
ORDER BY
  CASE collection_status
    WHEN 'overdue' THEN 1
    WHEN 'due_today' THEN 2
    WHEN 'never_collected' THEN 3
  END;
```

**Uso em código** (todos os places):
```javascript
// API
app.get('/api/pending', () => db.query('SELECT * FROM v_indicators_pending_collection'));

// Workflow n8n
SELECT * FROM v_indicators_pending_collection;

// Script de monitoramento
SELECT collection_status, COUNT(*) FROM v_indicators_pending_collection GROUP BY 1;
```

### Benefícios

- ✅ **Single Source of Truth**: Lógica centralizada
- ✅ **Testável via SQL**: Não precisa de código para testar
- ✅ **Reutilizável**: API, workflows, relatórios usam mesma view
- ✅ **Manutenível**: Mudança de lógica = alterar view (não 5 lugares no código)
- ✅ **Performance**: Views materializadas quando necessário
- ✅ **Versionável**: Views em migrations (git history completo)

### Views Materializadas (Performance)

Para lógica muito pesada:
```sql
CREATE MATERIALIZED VIEW mv_indicators_summary AS
SELECT
  dimension,
  COUNT(*) as total,
  COUNT(*) FILTER (WHERE last_ref_date IS NOT NULL) as collected,
  AVG(EXTRACT(days FROM (CURRENT_DATE - last_ref_date))) as avg_days_since_collection
FROM indicator_dictionary
GROUP BY dimension;

-- Refresh periodicamente
REFRESH MATERIALIZED VIEW mv_indicators_summary;
```

### Quando NÃO Usar

- ❌ Lógica muito simples (SELECT *  FROM table)
- ❌ Lógica muda constantemente (views exigem migrations)
- ❌ Performance crítica E view não é materializável

---

## 5. Workflow Naming Conventions

### Problema Descoberto

Durante desenvolvimento do Tocantins Integrado:
- Arquivo inicial: `Tocantins Integrado - Orquestrador.json` (com espaços)
- Quando segundo orquestrador foi adicionado: confusão
- "Qual é o orquestrador de análise? E o de coleta?"

### Solução: Convenção Clara

#### Regras

1. **Use hífens**, não espaços
2. **Seja específico**, não genérico
3. **Siga padrão**: `{funcao}-{especialidade}.json`
4. **Orquestradores**: `{funcao}-orchestrator.json`

#### Exemplos

**✅ BOM**:
```
analysis-orchestrator.json
data-collection-orchestrator.json
notification-orchestrator.json

data-collection-ibge.json
data-collection-inep.json
data-collection-mapbiomas.json

agent-econ.json
agent-social.json
agent-terra.json
```

**❌ RUIM**:
```
Tocantins Integrado - Orquestrador.json  # Espaços, nome do projeto
orchestrator.json                         # Genérico demais
orchestrator-2.json                       # Número não diz nada
workflow1.json                            # Zero contexto
final-version.json                        # Não descreve função
```

### Estrutura de Diretórios

Organize por tipo:
```
n8n/workflows/
├── orchestrators/
│   ├── analysis-orchestrator.json
│   └── data-collection-orchestrator.json
├── agents/
│   ├── agent-econ.json
│   ├── agent-social.json
│   ├── agent-terra.json
│   └── agent-ambient.json
└── collectors/
    ├── data-collection-ibge.json
    ├── data-collection-inep.json
    └── data-collection-mapbiomas.json
```

Ou flat (se poucos workflows):
```
n8n/workflows/
├── analysis-orchestrator.json
├── data-collection-orchestrator.json
├── agent-econ.json
├── data-collection-ibge.json
└── ...
```

### Benefícios

- ✅ **Clareza**: Nome descreve função
- ✅ **Ordenação**: Alfabética agrupa relacionados
- ✅ **Searchable**: Fácil encontrar via grep/search
- ✅ **Onboarding**: Novo dev entende estrutura rapidamente

---

## 🎯 Como Usar Este Documento

### Para Novos Projetos

1. **Leia todos os padrões** antes de começar arquitetura
2. **Identifique padrões aplicáveis** ao seu contexto
3. **Documente em ADR** se decidir usar (ou não usar) um padrão
4. **Referencie este documento** no ADR

### Para Projetos Existentes

1. **Revise arquitetura atual** contra esses padrões
2. **Identifique oportunidades** de refactoring
3. **Priorize** por dor atual (qual padrão resolve maior problema?)
4. **Implemente incrementalmente** (não refatore tudo de uma vez)

### Contribuindo com Novos Padrões

Padrão só entra neste documento se:
- ✅ Testado em projeto real
- ✅ Resolveu problema concreto
- ✅ Reutilizável em outros contextos
- ✅ Documentado com trade-offs

**Template para novo padrão**:
```markdown
## N. Nome do Padrão

### Quando Usar
[Lista de situações]

### Descrição
[Explicação clara]

### Implementação
[Código/exemplo]

### Exemplo Real
[Projeto real onde foi usado]

### Trade-offs
[Vantagens e desvantagens]
```

---

## 📚 Referências

- **Caso de Estudo**: [Tocantins Integrado](case-studies/01_TOCANTINS_INTEGRADO.md)
- **Framework**: [README.md](README.md)
- **Protocolos**: [protocols/](protocols/)

---

**Última atualização**: Janeiro 2026
**Projeto de origem**: Tocantins Integrado
**Contribuidores**: Henrique M. Ribeiro, Manus AI (CTO), Claude Code (Dev)
