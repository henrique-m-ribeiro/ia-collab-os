# Visão Geral dos Papéis

## O Problema da Sobrecarga Cognitiva

Em projetos de software tradicionais, um desenvolvedor único pode acumular múltiplos "chapéus":

- 🎯 **Estrategista**: Define o QUE construir e POR QUÊ
- 🏗️ **Arquiteto**: Decide COMO construir (design e estrutura)
- 💻 **Implementador**: Escreve o código
- 🧪 **Testador**: Valida funcionamento
- 📝 **Documentador**: Registra decisões e processo

Esta multiplicidade de papéis funciona para projetos pequenos, mas em sistemas complexos ou equipes humano-IA, gera **sobrecarga cognitiva** e **mistura de níveis de abstração**.

### Exemplo de Sobrecarga

Um desenvolvedor trabalhando sozinho pode:
- Estar debugando um problema de CSS (nível micro)
- Ao mesmo tempo pensando se a arquitetura de estado é escalável (nível macro)
- Enquanto tenta lembrar qual era o requisito original do cliente (nível estratégico)

**Resultado**: Contexto fragmentado, decisões subótimas, burnout cognitivo.

---

## A Solução: Separação de Responsabilidades

O **IA Collab OS** adota a estrutura organizacional clássica **CEO → CTO → Dev**, adaptada para equipes humano-IA:

```
┌─────────────────────────────────────────────────────┐
│                CEO (Humano)                          │
│  • Visão de Negócio                                 │
│  • Priorização de Valor                             │
│  • Validação de Entregas                            │
│  • Contexto de Usuário/Mercado                      │
└──────────────────┬──────────────────────────────────┘
                   │
         ┌─────────┴─────────┐
         │                   │
┌────────▼────────┐  ┌───────▼─────────┐
│  CTO (IA #1)    │  │   Dev (IA #2)   │
│  • Arquitetura  │  │  • Código       │
│  • Especificação│  │  • Testes       │
│  • ADRs         │  │  • Refactoring  │
│  • Riscos       │  │  • Debug        │
└─────────────────┘  └─────────────────┘
```

---

## Por Que Usar IAs em Papéis Diferentes?

### Limitações das IAs Atuais (2025-2026)

| Limitação | Impacto | Mitigação via Papéis |
|-----------|---------|----------------------|
| **Janela de contexto** | IAs "esquecem" após N tokens | Handoffs preservam memória |
| **Falta de visão de longo prazo** | Decisões míopes | CEO humano mantém estratégia |
| **Tendência a scope creep** | Adicionar features não pedidas | Papéis com escopos claros |
| **Ausência de julgamento de valor** | Não prioriza por impacto de negócio | CEO decide o QUE fazer |

### Vantagens de Usar IAs Diferentes

✅ **Especialização**: Cada IA opera em um nível de abstração
✅ **Memória distribuída**: Handoffs entre papéis = contexto preservado
✅ **Validação cruzada**: CTO revisa arquitetura, Dev questiona implementabilidade
✅ **Redução de viés**: IAs diferentes podem questionar decisões da outra

---

## Os Três Papéis

### 1. CEO (Humano)

**Essência**: O orquestrador que mantém visão de negócio e prioriza valor.

**Quando atua**:
- Início de projeto (visão)
- Início de sprint (priorização)
- Validação de entregas
- Desbloqueio de impedimentos

**Nível de abstração**: Estratégico

**Exemplo de Interação**:
```
CEO: "Precisamos de um dashboard para gestores públicos visualizarem
indicadores municipais. Foco em simplicidade - eles não são técnicos."
```

📄 **Detalhes**: [01_CEO_HUMAN.md](./01_CEO_HUMAN.md)

---

### 2. CTO (IA #1)

**Essência**: O arquiteto que traduz requisitos de negócio em especificações técnicas.

**Quando atua**:
- Após CEO definir o QUE fazer
- Análise de viabilidade
- Design de arquitetura
- Documentação de decisões (ADRs)
- Revisão de implementação

**Nível de abstração**: Tático/Arquitetural

**Exemplo de Interação**:
```
CTO: "Entendido. Proposta de arquitetura:
- Frontend: Next.js (SSR para SEO)
- Backend: Supabase (gerenciado, reduz tempo de MVP)
- Caching: React Query

Vou criar ADR-001 para escolha de Supabase vs backend próprio.
CEO, você aprova esta direção?"
```

📄 **Detalhes**: [02_CTO_AI.md](./02_CTO_AI.md)

---

### 3. Dev (IA #2)

**Essência**: O implementador que transforma especificações em código funcional.

**Quando atua**:
- Após CTO criar especificação aprovada
- Escrita de código
- Testes unitários/integração
- Debugging
- Refactoring
- Documentação inline

**Nível de abstração**: Operacional/Implementação

**Exemplo de Interação**:
```
Dev: "Li a spec. Vou implementar o MunicipalitySelector.
Decisão de implementação: Usar react-select para busca, dado
que lista tem 139 municípios. Criando componente..."

[2 horas depois]

Dev: "MunicipalitySelector implementado e testado.
Handoff criado com próximos passos (integrar com IndicatorTable).
Pronto para revisão do CTO."
```

📄 **Detalhes**: [03_DEV_AI.md](./03_DEV_AI.md)

---

## Fluxo de Comunicação

### Hierarquia de Decisões

```
Decisão de Negócio (O QUÊ)
    ↓
   CEO ←→ CTO (validação de viabilidade)
    ↓
Decisão Arquitetural (COMO em alto nível)
    ↓
   CTO → Dev (especificação)
    ↓
Decisão de Implementação (COMO em detalhe)
    ↓
   Dev (autonomia dentro do escopo)
    ↓
Se dúvida: Dev ← CTO ← CEO
```

### Regras de Comunicação

| Situação | Quem Fala | Com Quem | Formato |
|----------|-----------|----------|---------|
| Definir objetivos | CEO | → CTO | Requisitos de alto nível |
| Analisar viabilidade | CTO | → CEO | Análise de risco + estimativa |
| Especificar arquitetura | CTO | → Dev | Spec técnica + ADRs |
| Questionar spec | Dev | → CTO | Perguntas específicas |
| Reportar bloqueio | Dev | → CTO → CEO | Handoff com status |
| Validar entrega | CEO | ← CTO ← Dev | Demo + checklist |

---

## Quando Usar Um Papel vs Outro

### Decisão: Escolher Tecnologia

| Papel | Quando é Responsável |
|-------|---------------------|
| **CEO** | Se impacta custo, vendor lock-in ou habilidades do time |
| **CTO** | Se é decisão puramente técnica (ex: biblioteca de UI) |
| **Dev** | Se é detalhe de implementação (ex: nome de variável) |

**Exemplo**:
- "Usar AWS vs Azure" → CEO (impacto financeiro)
- "Usar PostgreSQL vs MongoDB" → CTO (arquitetural)
- "Usar lodash vs ramda" → Dev (implementação)

### Decisão: Mudança de Escopo

| Papel | Quando Decide |
|-------|---------------|
| **CEO** | Sempre (última palavra sobre escopo) |
| **CTO** | Pode sugerir redução se inviável |
| **Dev** | Pode alertar sobre complexidade inesperada |

---

## Configurando Papéis em Ferramentas de IA

### Claude (Anthropic)

**CEO (Humano)**: Você mesmo, sem prompt especial

**CTO (IA #1)**: Usar Claude Opus ou Sonnet 4 com prompt:
```
Você é o CTO (Chief Technology Officer) de um projeto de software.
Seu papel é traduzir requisitos de negócio em especificações técnicas,
fazer escolhas arquiteturais e documentá-las em ADRs.

Você NÃO escreve código - apenas especifica.
Você sempre pergunta antes de assumir requisitos.
```

**Dev (IA #2)**: Usar Claude Sonnet com Claude Code ou com prompt:
```
Você é o Desenvolvedor (Dev) de um projeto de software.
Seu papel é implementar especificações técnicas em código funcional.

Você segue especificações criadas pelo CTO.
Você pergunta ao CTO se algo na spec não está claro.
Você cria handoffs ao final de cada sessão.
```

### ChatGPT (OpenAI)

**CTO (IA #1)**: GPT-4 ou GPT-4 Turbo com prompt similar

**Dev (IA #2)**: GPT-4 com Code Interpreter ou Copilot Chat

---

## Antipadrões Comuns

### ❌ "Papel Único"

**Problema**: Usar uma IA para tudo (estratégia + arquitetura + código)

**Consequência**: Sobrecarga de contexto, decisões inconsistentes

**Solução**: Separar em pelo menos CEO (Humano) + Dev (IA). CTO pode ser humano ou IA dependendo do projeto.

### ❌ "Pular CTO"

**Problema**: CEO passa requisitos direto para Dev

**Consequência**: Decisões arquiteturais implícitas, retrabalho futuro

**Solução**: Sempre ter fase de especificação, mesmo que rápida

### ❌ "Dev Fazendo Arquitetura"

**Problema**: Dev toma decisões arquiteturais sem consultar CTO

**Consequência**: Arquitetura incoerente, difícil de manter

**Solução**: Dev deve alertar CTO quando encontrar necessidade de decisão arquitetural

### ❌ "CEO Microgerenciando"

**Problema**: CEO especifica detalhes de implementação

**Consequência**: Bottleneck, perde valor de especialização

**Solução**: CEO define O QUÊ e POR QUÊ, confia no CTO para COMO

---

## Papéis em Diferentes Tamanhos de Projeto

### Projeto Solo (1 pessoa)

```
Humano = CEO + CTO
IA = Dev
```

O humano faz visão E arquitetura, IA apenas implementa.

### Projeto Dupla (2 pessoas)

```
Humano 1 = CEO
Humano 2 = CTO
IA = Dev
```

Ou:
```
Humano = CEO
IA #1 = CTO
IA #2 = Dev
```

### Projeto Time (3+ pessoas)

```
Humano Lead = CEO
Humano Senior = CTO
Humano Junior + IA = Dev
```

Ou full-IA:
```
Humano = CEO
IA (Claude Opus) = CTO
IA (Claude Sonnet) = Dev
```

---

## Próximos Passos

Leia os documentos detalhados de cada papel:

1. [CEO (Humano)](./01_CEO_HUMAN.md)
2. [CTO (IA)](./02_CTO_AI.md)
3. [Dev (IA)](./03_DEV_AI.md)

Depois, veja os protocolos em [/protocols](../protocols/) para saber como os papéis se comunicam.
