# Princípios Fundamentais do IA Collab OS

Este documento estabelece os 5 princípios nucleares que governam o framework. Eles não são apenas regras, mas manifestações de uma filosofia epistemológica sobre como humanos e IAs devem colaborar efetivamente.

---

## 1. Documentação é Contrato

### Princípio

**Toda decisão relevante, especificação técnica e transferência de contexto deve ser documentada em artefatos formais e versionados.**

### Fundamentação

Em equipes humano-IA, a memória não pode ser presumida. As IAs operam em sessões com janelas de contexto limitadas, e humanos podem não lembrar de detalhes técnicos após dias ou semanas. A documentação não é um "extra" ou "overhead" – ela **É** o contrato que mantém a coerência do sistema.

### Implementação

| Artefato | Propósito | Quando Criar |
|----------|-----------|--------------|
| **Handoff** | Transferir contexto entre sessões | Ao final de cada sessão significativa |
| **ADR** (Architecture Decision Record) | Registrar decisões arquiteturais | Quando uma escolha técnica impacta o futuro |
| **Session Log** | Diário de atividades da sessão | Durante a sessão de trabalho |
| **Plan** | Roteiro detalhado de implementação | Antes de iniciar codificação |

### Anti-patterns

❌ **Confiar na memória**: "Já discutimos isso antes"
❌ **Documentação pós-facto**: Escrever após o código estar pronto
❌ **Comentários inline como documentação**: Código não substitui decisões de alto nível

---

## 2. Escopo Limitado

### Princípio

**Cada sessão de trabalho deve ter um objetivo claramente delimitado, com critérios de completude verificáveis.**

### Fundamentação

IAs generativas tendem a "expandir escopo" – começam com uma tarefa simples e adicionam melhorias não solicitadas. Isso consome contexto, aumenta superfície de bugs e dificulta a validação. O escopo deve ser um **hard constraint**, não uma sugestão.

### Implementação

**Estrutura de um Escopo Bem-Definido:**

```markdown
## Objetivo da Sessão
[Uma frase clara e específica]

## Entregas Esperadas
- [ ] Item verificável 1
- [ ] Item verificável 2
- [ ] Item verificável 3

## Fora do Escopo (Explícito)
- Feature X (será tratada na sessão Y)
- Otimização Z (não é prioritária)
```

**Técnicas para Manter Escopo:**

1. **CEO (Humano) define fronteiras** no início da sessão
2. **CTO (IA) valida escopo** e alerta sobre dependências
3. **Dev (IA) questiona** se uma tarefa está fora do escopo
4. **Handoff documenta** o que ficou pendente para próximas sessões

### Anti-patterns

❌ **Scope creep**: "Já que estamos aqui, vou adicionar..."
❌ **Perfeccionismo prematuro**: Otimizar antes de validar funcionamento
❌ **Tarefas implícitas**: Assumir que algo "obviamente" faz parte do escopo

---

## 3. Decisões Explícitas

### Princípio

**Toda escolha técnica ou de design com impacto futuro deve ser documentada com contexto, alternativas consideradas e justificativa.**

### Fundamentação

O valor de uma decisão não está apenas no resultado, mas no **raciocínio** que levou a ela. Quando contexto é perdido, decisões parecem arbitrárias ou erradas. ADRs (Architecture Decision Records) preservam a lógica, permitindo revisão futura informada.

### Implementação

**Estrutura de uma Decisão Explícita (ADR):**

| Seção | Conteúdo |
|-------|----------|
| **Contexto** | Qual problema ou necessidade motivou a decisão? |
| **Decisão** | O que foi decidido? (Em uma frase) |
| **Alternativas** | Quais outras opções foram consideradas? |
| **Consequências** | Quais trade-offs e impactos? |
| **Status** | Proposta / Aprovada / Depreciada / Superada |

**Exemplo de Decisão Explícita:**

```markdown
# ADR-003: Uso de Supabase para Backend

## Contexto
Precisamos de um backend gerenciado para persistência de dados,
auth e APIs. Temos restrições de tempo (MVP em 2 semanas) e orçamento inicial limitado.

## Decisão
Adotar Supabase (BaaS) ao invés de construir backend próprio com Node.js + PostgreSQL.

## Alternativas Consideradas
1. **Firebase**: Mais maduro, mas vendor lock-in maior e custo escalável
2. **Backend customizado**: Mais controle, mas 3-4x mais tempo de desenvolvimento
3. **Supabase**: Bom compromisso entre controle (SQL direto) e velocidade

## Consequências
✅ Reduz tempo de MVP em ~70%
✅ SQL nativo permite queries complexas
⚠️ Dependência de serviço terceiro (mitigável com self-hosting futuro)

## Status
Aprovada (2026-01-05)
```

### Anti-patterns

❌ **Decisões silenciosas**: Escolher tecnologia sem documentar o porquê
❌ **"Parece óbvio"**: Presumir que a lógica é auto-evidente
❌ **Revisitar sem contexto**: Mudar decisão sem entender o raciocínio original

---

## 4. CEO como Orquestrador

### Princípio

**O humano no papel de CEO não é apenas um "cliente" que dá instruções, mas o orquestrador ativo que mantém coerência estratégica e visão de negócio.**

### Fundamentação

IAs são excelentes em execução dentro de um escopo, mas não têm visão de longo prazo, priorização de valor de negócio ou capacidade de navegar trade-offs não-técnicos. O CEO humano é o "fio condutor" que garante que o sistema atenda objetivos reais, não apenas seja tecnicamente correto.

### Responsabilidades do CEO

| Área | Ação |
|------|------|
| **Visão** | Define objetivos de negócio e sucesso |
| **Priorização** | Decide o que fazer primeiro (backlog) |
| **Escopo** | Delimita fronteiras de cada sessão |
| **Validação** | Aprova entregas e decide próximos passos |
| **Desbloqueio** | Remove impedimentos não-técnicos |
| **Contexto de Negócio** | Fornece informações que IAs não têm (usuários, mercado, etc.) |

### O Que o CEO NÃO Faz

❌ **Especificar implementação**: CTO define arquitetura, Dev escreve código
❌ **Microgerenciar**: Confiar nos papéis especializados
❌ **Pular planejamento**: Sempre há fase de análise antes de execução

### Padrão de Interação

```
CEO (Humano):
"Precisamos de um dashboard para visualizar indicadores municipais.
Usuários são gestores públicos que precisam comparar cidades."

CTO (IA):
"Entendido. Vou elaborar arquitetura com:
1. API para buscar dados
2. Frontend com filtros e comparações
3. Sistema de caching
Você aprova esta direção antes de eu detalhar?"

CEO (Humano):
"Sim, mas caching pode ficar para fase 2. Foque em funcionalidade primeiro."

CTO (IA):
"Ajustado. Vou criar spec detalhada e passar para Dev implementar."
```

### Anti-patterns

❌ **CEO passivo**: "Faça o que achar melhor"
❌ **CEO técnico demais**: "Use Redux com middleware Thunk para estado..."
❌ **Falta de validação**: Aprovar sem entender as entregas

---

## 5. Handoffs Completos

### Princípio

**Toda transição de contexto (fim de sessão, mudança de papel, integração) deve ser acompanhada de um documento de Handoff estruturado que preserve decisões, estado atual e próximos passos.**

### Fundamentação

O maior "assassino" de produtividade em equipes humano-IA é a perda de contexto. Um desenvolvedor que retorna após 3 dias não lembra de todos os detalhes. Uma IA que inicia uma nova sessão não tem memória da anterior. Handoffs são o mecanismo de **serialização de contexto** que garante continuidade.

### Estrutura de um Handoff Completo

```markdown
# Handoff: [Título da Sessão]

## Contexto
- Objetivo da sessão
- Estado inicial do sistema

## Trabalho Realizado
- Lista de entregas completadas
- Decisões tomadas
- Problemas resolvidos

## Decisões Importantes
- ADRs criados ou referenciados
- Escolhas técnicas relevantes

## Estado Atual
- O que está funcionando?
- O que está pendente?
- Bloqueios ou impedimentos?

## Próximos Passos
- [ ] Tarefa 1 (prioridade alta)
- [ ] Tarefa 2 (prioridade média)
- [ ] Tarefa 3 (pode esperar)

## Contexto para Próxima Sessão
Informações críticas que a próxima IA/humano precisa saber
```

### Quando Criar Handoffs

| Situação | Handoff Necessário? |
|----------|---------------------|
| Final de sessão de trabalho > 1h | ✅ Sim |
| Mudança de papel (CTO → Dev) | ✅ Sim |
| Bloqueio que requer intervenção externa | ✅ Sim |
| Tarefa rápida < 30min | ⚠️ Opcional (use Session Log) |
| Fim de fase (ex: MVP completo) | ✅✅ Sim (Handoff detalhado) |

### Anti-patterns

❌ **Handoff vago**: "Fiz algumas correções"
❌ **Presumir óbvio**: "A próxima pessoa vai entender"
❌ **Handoff sem ação**: Não especificar próximos passos
❌ **Pular handoff por pressa**: "Não tenho tempo agora" (paga-se depois)

---

## Integração dos Princípios

Estes 5 princípios não são independentes – eles se reforçam mutuamente:

```
Documentação é Contrato
         ↓
    (garante)
         ↓
Decisões Explícitas + Handoffs Completos
         ↓
    (permitindo)
         ↓
CEO como Orquestrador
         ↓
    (mantendo)
         ↓
Escopo Limitado
         ↓
    (que facilita)
         ↓
Documentação é Contrato (ciclo virtuoso)
```

## Aplicando na Prática

Para internalizar estes princípios:

1. **Imprima e cole** este documento onde você trabalha
2. **Checklist de sessão**: Antes de começar, pergunte "Estou seguindo os 5 princípios?"
3. **Retrospectivas**: Ao final de cada sprint, avalie quais princípios foram violados
4. **Cultura, não coerção**: Princípios não são regras rígidas, mas valores guia

---

**Próximo**: Veja [METHODOLOGY.md](./METHODOLOGY.md) para o ciclo de trabalho completo.
