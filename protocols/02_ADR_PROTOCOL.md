# Protocolo de ADR (Architecture Decision Record)

## O Que É Um ADR?

Um **ADR** (Architecture Decision Record) é um documento que registra uma decisão arquitetural importante, incluindo contexto, alternativas consideradas e consequências.

## Por Que É Importante?

- **Rastreabilidade**: Saber POR QUÊ uma decisão foi tomada
- **Evita retrabalho**: Não revisitar decisões sem contexto
- **Onboarding**: Novos membros entendem escolhas passadas
- **Auditoria**: Registro formal de trade-offs

## Quando Criar Um ADR?

| Situação | ADR Necessário? |
|----------|-----------------|
| Escolha de framework principal | ✅ Sim |
| Decisão de banco de dados | ✅ Sim |
| Padrão arquitetural (monolito/microserviços) | ✅ Sim |
| Biblioteca UI (React/Vue) | ✅ Sim |
| Estrutura de pastas | ⚠️ Opcional |
| Nome de variável | ❌ Não |

**Regra geral**: Se a decisão impacta o futuro ou é difícil de reverter, crie um ADR.

## Estrutura Completa do ADR

```markdown
# ADR-XXX: [Título da Decisão]

## Status
[Proposta | Aprovada | Depreciada | Superada]

## Contexto
[Qual problema ou necessidade motivou esta decisão?]

## Decisão
[O que foi decidido? Uma frase clara]

## Alternativas Consideradas

### Opção 1: [Nome]
**Prós**: [lista]
**Contras**: [lista]

### Opção 2: [Nome]
**Prós**: [lista]
**Contras**: [lista]

### Opção 3 (Escolhida): [Nome]
**Prós**: [lista]
**Contras**: [lista]
**Por que escolhida**: [justificativa]

## Consequências

### Positivas
- [Benefício 1]
- [Benefício 2]

### Negativas
- [Trade-off 1 e mitigação]
- [Trade-off 2 e mitigação]

## Data
[YYYY-MM-DD]

## Participantes
- [Nome/Papel das pessoas envolvidas]
```

## Exemplo Completo

```markdown
# ADR-003: Uso de Supabase para Backend

## Status
Aprovada (2026-01-05)

## Contexto
O projeto Tocantins Integrado precisa de:
- Persistência de dados (indicadores municipais)
- Autenticação de usuários
- APIs REST para o frontend

Restrições:
- Prazo: MVP em 2 semanas
- Orçamento inicial limitado
- Time pequeno (1 dev + IAs)

## Decisão
Adotar Supabase (Backend-as-a-Service) ao invés de construir backend customizado com Node.js + PostgreSQL.

## Alternativas Consideradas

### Opção 1: Firebase
**Prós**: Muito maduro, ótima documentação, integração fácil
**Contras**: NoSQL (dificulta queries complexas), vendor lock-in forte, custo cresce rápido com escala

### Opção 2: Backend Customizado (Node.js + PostgreSQL)
**Prós**: Controle total, sem vendor lock-in, customizável
**Contras**: 3-4x mais tempo de desenvolvimento, precisa gerenciar infra, deploy mais complexo

### Opção 3 (Escolhida): Supabase
**Prós**: 
- PostgreSQL nativo (queries SQL complexas)
- Auth integrado
- Realtime subscriptions
- Self-hosting possível (mitiga lock-in)
- Tier gratuito generoso

**Contras**:
- Menos maduro que Firebase
- Documentação ainda em evolução

**Por que escolhida**: Melhor compromisso entre velocidade de desenvolvimento (70% mais rápido que opção 2) e controle (SQL direto, self-host possível).

## Consequências

### Positivas
- Reduz tempo de MVP de ~6 semanas para ~2 semanas
- Auth e CRUD prontos em horas, não dias
- SQL permite agregações complexas de indicadores
- Custo previsível até 50k usuários

### Negativas
- Dependência de serviço terceiro
  - **Mitigação**: Documentar schema SQL, preparar migração para self-host se necessário
- Limitações de customização de auth
  - **Mitigação**: Usar Row Level Security do Postgres para controle fino

## Data
2026-01-05

## Participantes
- Henrique (CEO): Aprovou baseado em trade-off tempo/custo
- Manus (CTO): Propôs e analisou alternativas
```

## Ciclo de Vida de Um ADR

```
Proposta → Discussão → Aprovada → [tempo] → Depreciada/Superada
```

- **Proposta**: CTO cria, aguarda validação do CEO
- **Aprovada**: CEO aprova, vira referência
- **Depreciada**: Decisão ainda válida mas não recomendada para novos projetos
- **Superada**: Nova decisão substitui esta (criar novo ADR referenciando o antigo)

## Numeração de ADRs

- Use números sequenciais: ADR-001, ADR-002, ADR-003...
- Nunca delete ou renumere (histórico é importante)
- Se decisão for superada, mantenha o ADR com status "Superada"

## Checklist de Qualidade

Um bom ADR deve responder:

- [ ] Qual era o problema/necessidade?
- [ ] Que alternativas foram consideradas?
- [ ] Por que esta opção foi escolhida?
- [ ] Quais são as consequências (boas e ruins)?
- [ ] Como mitigamos os trade-offs negativos?

**Próximo**: [Templates](../templates/)
