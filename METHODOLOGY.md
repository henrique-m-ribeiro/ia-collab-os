# Metodologia: Ciclo de Trabalho Completo

Este documento detalha o processo end-to-end de como equipes Humano-IA-IA colaboram usando o framework IA Collab OS. A metodologia opera em dois níveis: **Sprint Simplificado** (ciclo macro) e **Sessão Individual** (ciclo micro).

---

## Visão Geral

```
PROJETO
├── Sprint 1 (1-2 semanas)
│   ├── Sessão 1: Planejamento
│   ├── Sessão 2: Especificação
│   ├── Sessão 3-N: Implementação
│   └── Sessão Final: Validação
├── Sprint 2
│   └── ...
└── Sprint N
```

---

## Nível 1: Sprint Simplificado

Um **Sprint** é um ciclo de trabalho com objetivo claro e prazo definido (tipicamente 1-2 semanas). Cada sprint segue 5 fases obrigatórias:

### Fase 1: Planejamento

**Participantes**: CEO (Humano) + CTO (IA)

**Objetivo**: Definir o QUE será construído e POR QUÊ

**Atividades**:

| # | Ação | Responsável | Output |
|---|------|-------------|--------|
| 1 | Definir objetivo do sprint | CEO | Documento de Visão |
| 2 | Listar requisitos de alto nível | CEO | Lista de User Stories/Épicos |
| 3 | Priorizar backlog | CEO | Backlog ordenado |
| 4 | Analisar viabilidade técnica | CTO | Análise de Risco |
| 5 | Estimar escopo | CTO | Estimativa de sessões |
| 6 | Definir critérios de aceitação | CEO + CTO | Checklist de Done |

**Artefatos Criados**:
- `docs/sprints/sprint-N-plan.md`
- Atualização do backlog geral

**Exemplo de Output:**

```markdown
# Sprint 3: Dashboard de Indicadores Municipais

## Objetivo
Permitir que gestores públicos visualizem e comparem indicadores socioeconomicos de municípios do Tocantins.

## User Stories Priorizadas
1. Como gestor, quero filtrar por município para ver seus indicadores
2. Como gestor, quero comparar 2-3 municípios lado a lado
3. Como gestor, quero visualizar gráficos de evolução temporal

## Critérios de Aceitação
- [ ] Seletor de município funcional
- [ ] Tabela de comparação com 5+ indicadores
- [ ] Gráfico de linha para séries temporais
- [ ] Tempo de carregamento < 2s

## Estimativa
CTO: 6-8 sessões de desenvolvimento
```

---

### Fase 2: Especificação

**Participantes**: CTO (IA) liderando, CEO revisando

**Objetivo**: Definir COMO será construído (arquitetura e design)

**Atividades**:

| # | Ação | Responsável | Output |
|---|------|-------------|--------|
| 1 | Análise de contexto do projeto | CTO | Documento de Estado Atual |
| 2 | Design de arquitetura | CTO | Diagrama de componentes |
| 3 | Escolhas tecnológicas | CTO | ADRs para decisões-chave |
| 4 | Especificação de APIs/interfaces | CTO | Contratos de interface |
| 5 | Identificação de dependências | CTO | Grafo de dependências |
| 6 | Plano de implementação detalhado | CTO | Roteiro passo-a-passo |
| 7 | Revisão e aprovação | CEO | Aprovação formal |

**Artefatos Criados**:
- `docs/architecture/spec-sprint-N.md`
- ADRs relevantes em `docs/decisions/`
- Diagramas em `docs/diagrams/`

**Template de Especificação**:

```markdown
# Especificação Técnica: [Título]

## Contexto
Estado atual do sistema e motivação para mudança

## Arquitetura Proposta
Diagrama e descrição de componentes

## Decisões Arquiteturais
- ADR-XXX: [Título da decisão]
- ADR-YYY: [Título da decisão]

## Interfaces
### API Endpoints
```http
GET /api/municipalities
POST /api/indicators/compare
```

### Componentes React
```typescript
<MunicipalitySelector onSelect={...} />
<IndicatorTable data={...} />
```

## Plano de Implementação
1. [ ] Tarefa 1 - Estimativa: 1h
2. [ ] Tarefa 2 - Estimativa: 2h
...

## Riscos e Mitigações
- Risco: Performance com muitos dados → Mitigação: Paginação
```

---

### Fase 3: Implementação

**Participantes**: Dev (IA) executando, CTO ocasionalmente para decisões

**Objetivo**: Traduzir especificação em código funcional

**Atividades**:

| # | Ação | Responsável | Output |
|---|------|-------------|--------|
| 1 | Revisar especificação | Dev | Entendimento confirmado |
| 2 | Implementar feature incremental | Dev | Código + testes |
| 3 | Documentar decisões de implementação | Dev | Comentários + ADRs se necessário |
| 4 | Testar localmente | Dev | Suite de testes passando |
| 5 | Criar handoff ao final da sessão | Dev | Handoff document |

**Estrutura de uma Sessão de Implementação**:

```markdown
## Sessão de Implementação

### Input
- Especificação: docs/architecture/spec-sprint-3.md
- Tarefa específica: Implementar MunicipalitySelector

### Trabalho Realizado
- Criado componente em src/components/MunicipalitySelector.tsx
- Integrado com API /api/municipalities
- Adicionado loading state e error handling
- Testes unitários para cenários principais

### Decisões de Implementação
- Usamos React Query para caching (evitar refetch)
- Dropdown com busca (lib: react-select) para UX

### Próximos Passos
- [ ] Integrar MunicipalitySelector com IndicatorTable
- [ ] Adicionar filtro por microrregião
```

**Boas Práticas**:

✅ **Commits atômicos**: Um commit = uma unidade lógica de mudança
✅ **Testes junto com código**: Não deixar para depois
✅ **Handoff a cada 2-3h**: Não deixar contexto acumular
✅ **Perguntar quando em dúvida**: Melhor clarificar com CTO do que assumir

---

### Fase 4: Validação

**Participantes**: CEO (Humano) + CTO (IA) + Dev (IA) se necessário

**Objetivo**: Verificar que o produto atende critérios de aceitação

**Atividades**:

| # | Ação | Responsável | Output |
|---|------|-------------|--------|
| 1 | Executar checklist de aceitação | CEO | Lista com ✅/❌ |
| 2 | Testar cenários de uso real | CEO | Bugs identificados |
| 3 | Validar performance | CTO | Métricas (tempo de resposta, etc.) |
| 4 | Revisão de código | CTO | Code review comments |
| 5 | Ajustes e correções | Dev | Fixes implementados |
| 6 | Aprovação final | CEO | Go/No-Go para produção |

**Checklist de Validação**:

```markdown
## Validação Sprint 3

### Funcionalidade
- [x] Seletor de município carrega lista completa
- [x] Filtro de busca funciona corretamente
- [x] Tabela de comparação exibe 5+ indicadores
- [x] Gráfico renderiza série temporal
- [ ] ⚠️ Bug: Gráfico não atualiza ao trocar município

### Performance
- [x] Carregamento inicial < 2s
- [x] Busca responde em < 300ms
- [x] Sem memory leaks (testado com DevTools)

### UX
- [x] Interface intuitiva (testado com usuário piloto)
- [ ] ⚠️ Melhorar: Loading spinner não aparece em slow 3G

### Técnico
- [x] Code review aprovado por CTO
- [x] Testes unitários passando (95% coverage)
- [x] Sem warnings no console

## Decisão
✅ Aprovado com 2 bugs não-bloqueantes (criar issues para próximo sprint)
```

---

### Fase 5: Retrospectiva

**Participantes**: CEO + CTO + Dev (todos)

**Objetivo**: Aprender com o sprint e melhorar processo

**Atividades**:

| # | Pergunta | Output |
|---|----------|--------|
| 1 | O que funcionou bem? | Lista de sucessos |
| 2 | O que pode melhorar? | Lista de problemas |
| 3 | Quais princípios foram violados? | Análise de desvios |
| 4 | Ações concretas para próximo sprint | Action items |

**Template de Retrospectiva**:

```markdown
# Retrospectiva Sprint 3

## ✅ O Que Funcionou Bem
- Especificação detalhada economizou tempo na implementação
- Handoffs frequentes evitaram perda de contexto
- Comunicação CEO-CTO foi clara e objetiva

## ⚠️ O Que Pode Melhorar
- Estimativa de tempo foi otimista (previsto: 6 sessões, real: 9 sessões)
- Faltou considerar tempo de debugging na estimativa
- Dev fez mudanças de escopo sem consultar CTO

## 🎯 Ações para Próximo Sprint
- [ ] CTO: Adicionar 30% de buffer em estimativas
- [ ] Dev: Criar ADR para decisões de implementação não-triviais
- [ ] CEO: Validar incrementalmente (a cada 2 dias) ao invés de só no fim

## Métricas
- Sessões: 9 (estimado: 6)
- Bugs em validação: 2 (não-bloqueantes)
- ADRs criados: 4
- Handoffs: 9
```

---

## Nível 2: Sessão Individual

Uma **Sessão** é uma unidade de trabalho com início e fim claros (tipicamente 1-4 horas). Toda sessão segue 3 fases:

### Abertura: Carregar Contexto

**Duração**: 5-15 minutos

**Ações**:

1. **Ler handoff anterior** (se existir)
2. **Revisar documentos relevantes** (specs, ADRs)
3. **Confirmar objetivo da sessão**
4. **Verificar estado do sistema** (git status, testes, etc.)

**Checklist de Abertura**:

```markdown
- [ ] Li o handoff da sessão anterior
- [ ] Entendo o objetivo desta sessão
- [ ] Tenho acesso a todos os recursos necessários
- [ ] Confirmo que o sistema está no estado esperado
```

---

### Execução: Fazer o Trabalho

**Duração**: 80% do tempo da sessão

**Práticas**:

✅ **Foco no escopo**: Resistir à tentação de "melhorar outras coisas"
✅ **Documentar durante**: Não deixar para o final
✅ **Commits frequentes**: A cada unidade lógica completada
✅ **Testar incrementalmente**: Não acumular código não-testado

**Anti-patterns**:

❌ **"Só mais uma coisinha"**: Scope creep micro
❌ **Commit gigante no final**: Difícil de revisar
❌ **"Vou documentar depois"**: Nunca documenta
❌ **Trabalhar sem validação**: Descobrir bugs só no fim

---

### Encerramento: Preservar Contexto

**Duração**: 10-20% do tempo da sessão

**Ações**:

1. **Criar handoff** usando o template
2. **Commit final** com mensagem descritiva
3. **Push para repositório**
4. **Atualizar status** de tarefas (se houver board)
5. **Documentar bloqueios** (se houver)

**Template de Handoff Rápido**:

```markdown
# Handoff: [Nome da Tarefa]

## Completado
- [x] Item 1
- [x] Item 2

## Próximo
- [ ] Item 3 (começa aqui)

## Observações
Contexto relevante para próxima sessão
```

---

## Fluxos Especiais

### Fluxo: Bloqueio

```
Dev encontra bloqueio
    ↓
Documentar em handoff
    ↓
Notificar CEO/CTO
    ↓
CEO decide: escalar, mudar escopo ou esperar
    ↓
Atualizar plano
```

### Fluxo: Mudança de Escopo

```
Descoberta de nova complexidade
    ↓
Dev/CTO alerta CEO
    ↓
CEO decide: aceitar, adiar ou cancelar feature
    ↓
Se aceitar: CTO reestima e atualiza spec
    ↓
Documentar em ADR a mudança
```

### Fluxo: Decisão Arquitetural

```
Dev identifica necessidade de decisão
    ↓
Consultar CTO (não decidir sozinho)
    ↓
CTO analisa e propõe solução
    ↓
Se impacto em negócio: CEO valida
    ↓
Documentar em ADR
    ↓
Dev implementa
```

---

## Métricas de Sucesso

Como saber se a metodologia está funcionando?

| Métrica | Alvo | Como Medir |
|---------|------|------------|
| **Handoffs por semana** | ≥ 5 | Contar arquivos em docs/handoffs/ |
| **ADRs por sprint** | ≥ 2 | Contar novos ADRs |
| **Taxa de retrabalho** | < 20% | Commits que desfazem trabalho anterior |
| **Sessões com escopo claro** | 100% | Revisar handoffs |
| **Bugs em validação** | < 5 | Contar issues encontradas pelo CEO |

---

## Adaptações por Tamanho de Projeto

### Projeto Pequeno (< 1 semana)

- ⚡ Sprint único
- ⚡ Especificação simplificada (pode ser só uma lista)
- ⚡ Handoffs opcionais (mas session logs obrigatórios)
- ✅ ADRs apenas para decisões críticas

### Projeto Médio (1-4 semanas)

- ⚡ 2-4 sprints
- ✅ Especificação completa
- ✅ Handoffs obrigatórios
- ✅ Retrospectivas a cada sprint

### Projeto Grande (> 1 mês)

- ✅ Sprints regulares
- ✅ Especificações detalhadas com diagramas
- ✅ Handoffs + Session logs
- ✅ Retrospectivas + métricas de processo
- ✅ Revisões de arquitetura periódicas

---

## Checklist de Implementação

Para adotar esta metodologia em um novo projeto:

**Antes de Começar**:
- [ ] Ler e internalizar os 5 princípios
- [ ] Definir quem é CEO, CTO e Dev
- [ ] Criar estrutura de pastas: docs/{sprints, architecture, decisions, handoffs}
- [ ] Copiar templates para o projeto

**Durante o Projeto**:
- [ ] Começar sempre com planejamento (não pular para código)
- [ ] Criar handoff ao final de cada sessão significativa
- [ ] Documentar decisões arquiteturais em ADRs
- [ ] Fazer retrospectivas regulares

**Sinais de Alerta**:
- 🚨 Mais de 3 sessões sem handoff
- 🚨 Decisões técnicas sem ADR
- 🚨 Retrabalho frequente (>30% do tempo)
- 🚨 CEO não sabe o estado atual do projeto
- 🚨 Dev fazendo mudanças de escopo sem consulta

---

**Próximo**: Veja os papéis específicos em [roles/](./roles/) para entender responsabilidades detalhadas.
