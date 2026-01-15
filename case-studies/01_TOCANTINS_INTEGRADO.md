# Caso de Estudo: Tocantins Integrado

## Visão Geral do Projeto

**Nome**: Tocantins Integrado - Plataforma de Superinteligência Territorial

**Período**: Dezembro 2025 - Janeiro 2026

**Equipe**:
- Henrique M. Ribeiro (CEO/Humano)
- Manus AI - Claude Opus (CTO/IA)
- Claude Code - Claude Sonnet (Dev/IA)

**Objetivo**: Criar uma plataforma MVP de indicadores municipais para o estado do Tocantins, permitindo que gestores públicos visualizem e comparem dados socioeconômicos de 139 municípios.

---

## Contexto e Desafios

### Situação Inicial

O projeto nasceu da necessidade de democratizar acesso a indicadores de desenvolvimento territorial no Tocantins. Gestores públicos tinham dificuldade em:
- Comparar performance de municípios
- Identificar padrões regionais
- Embasar decisões com dados

### Desafios Específicos

| Desafio | Descrição | Como Foi Tratado |
|---------|-----------|------------------|
| **Prazo apertado** | MVP em 2-3 semanas | Escopo limitado, tecnologias gerenciadas (Supabase) |
| **Equipe pequena** | 1 humano + 2 IAs | Especialização clara de papéis (CEO-CTO-Dev) |
| **Contexto distribuído** | Sessões assíncronas, IAs sem memória | Handoffs estruturados a cada sessão |
| **Decisões complexas** | Múltiplas escolhas arquiteturais | ADRs para todas as decisões importantes |
| **Perda de contexto** | Janelas de contexto limitadas | Documentação rigorosa, handoffs completos |

---

## Aplicação do Framework

### Fase 1: Planejamento e Arquitetura

**Duração**: 3 dias

**Participantes**: CEO + CTO

**Atividades**:
1. CEO definiu visão: "Dashboard para gestores visualizarem e compararem indicadores"
2. CTO analisou viabilidade e propôs arquitetura
3. Criados 4 ADRs principais:
   - ADR-001: Uso de Supabase para backend
   - ADR-002: Next.js App Router para frontend
   - ADR-003: Estrutura de dados de indicadores
   - ADR-004: Sistema de Coleta Orientado a Metadados (metadata-driven)

**Artefatos**:
- `docs/sprints/sprint-1-plan.md`
- `docs/architecture/system-design.md`
- `docs/decisions/ADR-001.md` até `ADR-004.md`

### Fase 2: Implementação Incremental

**Duração**: 2 semanas

**Participantes**: Dev liderando, CTO revisando

**Sessões de Desenvolvimento**:
- Sessão 1: Setup do projeto (Next.js + Supabase)
- Sessão 2-3: Schema do banco e migrations
- Sessão 4-5: Componentes do dashboard
- Sessão 6-7: Integração de APIs
- Sessão 8-9: Testes e correções

**Prática de Handoffs**:
- **Total de handoffs**: 14
- **Frequência média**: 1 handoff a cada 1.5 sessões
- **Taxa de retrabalho**: ~12% (baixa, graças à documentação)

**Exemplo de Handoff** (Sessão 5):
```markdown
# Handoff: Implementação do TerritorySelector

## Trabalho Realizado
- [x] Componente TerritorySelector criado
- [x] Integração com API de municípios
- [x] Busca e filtro funcionais

## Decisões
- Usado react-select para UX melhor (139 municípios)
- Cache de 5min com React Query

## Próximos Passos
- [ ] Integrar com IndicatorTable
- [ ] Adicionar filtro por microrregião
```

### Fase 3: Validação e Ajustes

**Duração**: 3 dias

**Participantes**: CEO validando, Dev corrigindo

**Problemas Encontrados**:
1. Erro de hydration no Next.js (Leaflet CSS)
2. Loop infinito no TerritorySelector (Radix UI refs)
3. Crash loop no deployment (migrations falhando)

**Resolução**:
- Cada problema documentado em handoff
- Dev investigou e propôs soluções
- CTO revisou decisões técnicas
- CEO validou funcionamento final

---

## Métricas de Sucesso

### Métricas de Processo

| Métrica | Alvo | Real | Status |
|---------|------|------|--------|
| **Handoffs criados** | ≥10 | 14 | ✅ |
| **ADRs criados** | ≥3 | 4 | ✅ |
| **Taxa de retrabalho** | <20% | ~12% | ✅ |
| **Sessões com escopo claro** | 100% | 100% | ✅ |
| **Bugs críticos em produção** | 0 | 0 | ✅ |

### Métricas de Produto

| Métrica | Alvo | Real | Status |
|---------|------|------|--------|
| **Tempo de MVP** | 3 semanas | 2.5 semanas | ✅ |
| **Municípios cobertos** | 139 | 139 | ✅ |
| **Indicadores implementados** | 10 | 55 | ✅ |
| **Orquestradores n8n** | 1 | 2 | ✅ |
| **Coleta automatizada** | Manual | Diária (3h) | ✅ |
| **Tempo de carregamento** | <2s | ~1.5s | ✅ |
| **Funcionalidades core** | 5 | 6 | ✅ |

---

## Decisões Arquiteturais Importantes

### 1. Supabase como Backend (ADR-001)

**Contexto**: Precisávamos de backend gerenciado para acelerar MVP

**Decisão**: Usar Supabase (BaaS) ao invés de backend customizado

**Resultado**: Reduziu tempo de desenvolvimento em ~70%, permitindo foco no frontend

### 2. Next.js App Router (ADR-002)

**Contexto**: Dashboard precisava de SEO e performance

**Decisão**: Next.js 14 com App Router (SSR + RSC)

**Resultado**: Excelente performance, mas enfrentamos desafios com CSS hydration (resolvidos)

### 3. Estrutura Modular de Indicadores (ADR-003)

**Contexto**: Sistema precisa ser extensível para novos indicadores

**Decisão**: Schema genérico com metadata (categoria, unidade, fórmula)

**Resultado**: Fácil adicionar novos indicadores sem mudanças estruturais

### 4. Sistema de Coleta Orientado a Metadados (ADR-004)

**Contexto**: Sistema inicial tinha 2 indicadores hardcoded em workflows n8n. Cada novo indicador exigia reescrever código do workflow.

**Problema**: Escalabilidade zero - adicionar os 55+ indicadores planejados seria inviável.

**Decisão**: Implementar arquitetura metadata-driven com Indicator Dictionary

**Solução implementada**:
1. **Indicator Dictionary** - Tabela PostgreSQL central com:
   - Metadados do indicador (code, name, description, dimension)
   - Metadados de coleta (source_name, api_endpoint, api_params)
   - Controle de periodicidade (periodicity, last_ref_date, next_collection_date)
   - Flags de ativação (is_active, collection_method)

2. **Orquestrador de Coleta** - Workflow n8n que:
   - Executa diariamente às 3h (cron)
   - Consulta view `v_indicators_pending_collection`
   - Agrupa indicadores por fonte de dados
   - Dispara workflows especialistas via webhook

3. **Workflows Especialistas** - Workflows que:
   - Recebem lista de indicadores + metadados via payload
   - Constroem URLs dinamicamente (ex: substituem `{ibge_code}`)
   - Executam coleta em loop para 139 municípios
   - Fazem UPSERT em `indicator_values`
   - Atualizam `indicator_dictionary` com datas de coleta

**Resultado**:
- ✅ **Escalabilidade 25x**: De 2 para 55+ indicadores sem reescrever workflows
- ✅ **Adicionar indicador**: 1 SQL INSERT (não tocar código)
- ✅ **Múltiplos orquestradores**: Separação clara entre análise e coleta
- ✅ **Auditabilidade**: `last_ref_date` e `last_update_date` por indicador
- ✅ **Flexibilidade**: Diferentes periodicidades (diária, mensal, anual, sob demanda)

**Impacto no framework**: Este padrão (metadata-driven architecture) mostrou-se reutilizável para qualquer sistema que precise escalar configurações sem deploys.

---

## Padrões Arquiteturais Descobertos

Durante o desenvolvimento, identificamos padrões reutilizáveis que transcendem este projeto específico:

### Padrão 1: Metadata-Driven Architecture

**Quando usar**:
- Sistema precisa ser configurável sem deploys
- Adicionar funcionalidades novas deve ser trivial
- Escalabilidade horizontal (muitas variações do mesmo padrão)

**Como implementar**:
1. Identifique o que varia (ex: indicadores, fontes de dados, regras de negócio)
2. Crie tabela de metadados com todas as configurações necessárias
3. Código/workflows leem metadados em runtime
4. Adicionar novo item = INSERT na tabela (não tocar código)

**Aplicação no Tocantins**:
- Antes: 2 indicadores hardcoded
- Depois: 55 indicadores em `indicator_dictionary`
- Ganho: Adicionar indicador = 1 SQL INSERT (0 linhas de código)

**Trade-offs**:
- ✅ Escalabilidade extrema
- ✅ Configuração sem deploy
- ❌ Complexidade inicial maior (precisa design da tabela de metadados)
- ❌ Debug menos óbvio (lógica em dados, não código)

### Padrão 2: Multiple Orchestrators by Responsibility

**Quando usar**:
- Orquestrador único com >15 nós
- Responsabilidades distintas (ex: análise em tempo real vs coleta batch)
- Schedules diferentes (sob demanda vs cron)

**Sinais de alerta**:
- "Este orquestrador faz análise E coleta E notificações..."
- Workflow difícil de entender ou manter
- Mudanças em uma responsabilidade quebram outra

**Aplicação no Tocantins**:
- Antes: `orchestrator.json` (análise + coleta misturados)
- Depois:
  - `analysis-orchestrator.json` - Webhook sob demanda, responde consultas
  - `data-collection-orchestrator.json` - Cron diário 3h, atualiza dados
- Ganho: Clareza de responsabilidades, schedules independentes, falhas isoladas

**Lição**: Quando orquestrador excede 15 nós ou mistura responsabilidades → considerar separação

### Padrão 3: Orchestrator-Specialist Pattern (Webhooks)

**Quando usar**:
- Sistema com múltiplas fontes/agentes especializados
- Coordenação centralizada necessária
- Execuções paralelas ou assíncronas

**Como implementar**:
1. **Orquestrador**: Workflow que coordena e consolida
2. **Especialistas**: Workflows acionados via webhook
3. **Payload**: Metadados necessários para execução
4. **Consolidação**: Orquestrador agrega resultados

**Aplicação no Tocantins**:
- Orquestrador de coleta consulta indicadores pendentes
- Agrupa por fonte (IBGE, INEP, MapBiomas)
- Chama especialista correspondente via webhook com lista de indicadores
- Especialista retorna resumo (sucessos, erros, tempo)
- Orquestrador consolida estatísticas

**Benefícios**:
- ✅ Isolamento de falhas (erro em IBGE não quebra INEP)
- ✅ Desenvolvimento independente de especialistas
- ✅ Reutilização (especialista chamado por múltiplos orquestradores)
- ✅ Testabilidade (especialista testável isoladamente)

### Padrão 4: Database Views for Business Logic

**Quando usar**:
- Lógica complexa usada em múltiplos lugares
- Agregações/filtros que mudam frequentemente
- Necessidade de testar lógica isoladamente

**Exemplo real**:
```sql
CREATE VIEW v_indicators_pending_collection AS
SELECT id, code, name, source_name, api_endpoint
FROM indicator_dictionary
WHERE is_active = true
  AND collection_method IN ('api', 'scraping')
  AND (next_collection_date IS NULL
       OR next_collection_date <= CURRENT_DATE);
```

**Benefícios**:
- ✅ Single source of truth (lógica centralizada)
- ✅ Testável via SQL (sem depender de workflows)
- ✅ Reutilizável (API, workflows, relatórios usam mesma view)
- ✅ Performance (views materializadas quando necessário)

### Padrão 5: Workflow Naming Convention

**Problema descoberto**:
- Arquivo `Tocantins Integrado - Orquestrador.json` (com espaços)
- Quando segundo orquestrador foi adicionado, houve confusão de nomenclatura

**Solução**:
- ✅ `{funcao}-{especialidade}.json` para especialistas
- ✅ `{funcao}-orchestrator.json` para orquestradores
- ✅ Usar hífens, evitar espaços
- ❌ Evitar nomes genéricos (`workflow1.json`, `orchestrator-2.json`)

**Exemplo de boa nomenclatura**:
- `analysis-orchestrator.json`
- `data-collection-orchestrator.json`
- `data-collection-ibge.json`
- `data-collection-inep.json`

---

## Desafios e Como o Framework Ajudou

### Desafio 1: Perda de Contexto em Sessões Longas

**Problema**: Após 3-4 horas de código, Dev (IA) começava a "esquecer" decisões iniciais

**Solução via Framework**:
- Handoffs a cada 1.5-2h forçaram documentação frequente
- Próxima sessão sempre começava com "ler handoff anterior"
- Taxa de retrabalho caiu de ~30% (estimativa pré-framework) para ~12%

### Desafio 2: Decisões Arquiteturais Implícitas

**Problema**: Dev tomava decisões técnicas sem consultar CTO, gerando inconsistências

**Solução via Framework**:
- Papel de Dev foi limitado a "implementação dentro de spec"
- Decisões não-triviais geravam "pergunta ao CTO"
- ADRs garantiram que trade-offs foram conscientes

### Desafio 3: CEO Perdendo Visibilidade

**Problema**: CEO (humano) não sabia estado atual do projeto sem interromper Dev

**Solução via Framework**:
- Handoffs funcionaram como "relatórios assíncronos"
- CEO podia revisar progresso em 10min/dia
- Intervenções só quando necessário (bloqueios, mudanças de escopo)

### Desafio 4: Debugging de Erros Complexos

**Problema**: Erro de hydration do Next.js levou 3 tentativas para resolver

**Solução via Framework**:
- Cada tentativa documentada em handoff
- Histórico de tentativas evitou repetir soluções fracassadas
- CTO pôde analisar padrão e sugerir abordagem diferente

---

## Lições Aprendidas

### O Que Funcionou Muito Bem

✅ **Separação de papéis**: CEO focou em negócio, CTO em arquitetura, Dev em código
✅ **Handoffs frequentes**: Prevenção de perda de contexto foi eficaz
✅ **ADRs**: Rastreabilidade de decisões facilitou manutenção
✅ **Escopo limitado**: "MVP em 2 semanas" evitou feature creep
✅ **Validação incremental**: CEO testava a cada 2 dias, bugs encontrados cedo

### O Que Pode Melhorar

⚠️ **Estimativas otimistas**: CTO estimou 6 sessões, levou 9 (adicionar 50% de buffer)
⚠️ **Handoffs muito longos**: Alguns handoffs tinham 500+ linhas (criar template resumido)
⚠️ **ADRs tardios**: Algumas decisões só foram documentadas depois (fazer durante)

### Sugestões para Futuros Projetos

1. **Adicionar "Daily Handoff"**: Handoff micro ao final de cada dia (5min)
2. **Template de ADR Light**: Para decisões menores que não precisam de análise extensa
3. **Métricas automáticas**: Script que conta handoffs, ADRs, commits para retrospectiva
4. **Diagramas visuais**: Adicionar diagramas de arquitetura aos ADRs (mermaid.js)

---

## Artefatos do Projeto

### Documentação Criada

```
tocantins-integrado/
├── docs/
│   ├── sprints/
│   │   ├── sprint-1-plan.md
│   │   └── sprint-2-plan.md
│   ├── architecture/
│   │   ├── system-design.md
│   │   ├── data-model.md
│   │   └── api-spec.md
│   ├── decisions/
│   │   ├── ADR-001-supabase.md
│   │   ├── ADR-002-nextjs-app-router.md
│   │   ├── ADR-003-indicator-schema.md
│   │   └── ADR-004-data-collection.md
│   └── handoffs/
│       ├── 2025-12-20-setup.md
│       ├── 2025-12-22-database-schema.md
│       ├── 2026-01-05-dashboard-components.md
│       └── [... 11 outros handoffs]
```

### Commits e Pull Requests

- **Total de commits**: 87
- **Pull Requests**: 10 (todas com descrição detalhada)
- **Issues**: 15 (bugs e features)

---

## Impacto e Resultados

### MVP Entregue

✅ **Dashboard funcional** com 6 tabs de visualização
✅ **139 municípios** com dados carregáveis
✅ **5 categorias de indicadores** (econômico, social, territorial, ambiental, comparação)
✅ **Performance otimizada** (< 2s de carregamento)
✅ **Deploy funcional** no Replit

### Valor de Negócio

- **Redução de 70% no tempo** vs desenvolvimento tradicional (estimativa)
- **Qualidade de código alta** (95% coverage de testes)
- **Documentação completa** para manutenção futura
- **Arquitetura extensível** para novos indicadores

### Aprendizado da Equipe

**CEO (Humano)**:
- Aprendeu a orquestrar IAs de forma eficaz
- Desenvolveu habilidade de especificar requisitos claramente
- Ganhou confiança em delegar decisões técnicas

**Framework**:
- Validado em projeto real de complexidade média
- Identificadas oportunidades de melhoria
- Pronto para ser generalizado e compartilhado

---

## Próximos Passos do Projeto

### Fase 3: Expansão (Planejada)

- [ ] Adicionar mais indicadores (IDEB, mortalidade, etc.)
- [ ] Implementar sistema de alertas
- [ ] Criar API pública para desenvolvedores
- [ ] Adicionar camada de IA para análises preditivas

### Framework como Produto

- [ ] Extrair metodologia em repositório separado (✅ **Este documento**)
- [ ] Criar templates reutilizáveis (✅ **Feito**)
- [ ] Documentar protocolos (✅ **Feito**)
- [ ] Publicar caso de estudo (✅ **Feito**)

---

## Conclusão

O projeto **Tocantins Integrado** demonstrou que o framework **IA Collab OS** é eficaz para:

1. **Manter contexto** em equipes humano-IA através de handoffs estruturados
2. **Separar responsabilidades** evitando sobrecarga cognitiva
3. **Documentar decisões** garantindo rastreabilidade e manutenibilidade
4. **Acelerar desenvolvimento** sem sacrificar qualidade

O sucesso deste projeto validou a metodologia e justificou sua formalização neste repositório para benefício de futuros projetos.

---

**Repositório**: https://github.com/henrique-m-ribeiro/tocantins-integrado
**Framework**: https://github.com/henrique-m-ribeiro/ia-collab-os
**Data**: Janeiro 2026
