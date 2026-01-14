# Protocolo de Handoff

## O Que É Um Handoff?

Um **Handoff** é um documento estruturado que transfere contexto completo de uma sessão de trabalho para a próxima. É o mecanismo de "serialização de memória" que garante continuidade em equipes humano-IA.

## Por Que É Crucial?

- **IAs não têm memória persistente** entre sessões
- **Humanos esquecem detalhes** após dias/semanas
- **Decisões implícitas se perdem** sem documentação
- **Retrabalho é custoso** quando contexto é perdido

## Quando Criar Um Handoff?

| Situação | Handoff Necessário? |
|----------|---------------------|
| Final de sessão > 1h | ✅ Sim |
| Mudança de papel (CTO → Dev) | ✅ Sim |
| Bloqueio que requer intervenção | ✅ Sim |
| Fim de fase/sprint | ✅✅ Sim (detalhado) |
| Tarefa rápida < 30min | ⚠️ Opcional |

## Estrutura Completa do Handoff

```markdown
# Handoff: [Título da Sessão]

## Contexto
- Objetivo inicial da sessão
- Estado do sistema no início
- Documentos relevantes (specs, ADRs)

## Trabalho Realizado
- [x] Item 1 completado
- [x] Item 2 completado
- [ ] Item 3 iniciado (60% pronto)

## Decisões Importantes
- Decisão X: Escolhido Y porque Z
- ADR-003 criado: [link]

## Estado Atual
- O que está funcionando: [lista]
- O que está pendente: [lista]
- Bloqueios: [se houver]

## Próximos Passos
- [ ] Tarefa A (prioridade alta)
- [ ] Tarefa B (prioridade média)

## Contexto para Próxima Sessão
[Informações críticas que a próxima pessoa/IA precisa saber]
```

## Exemplos

### ✅ Bom Handoff

```markdown
# Handoff: Implementação do MunicipalitySelector

## Contexto
Objetivo: Implementar componente de seleção de município conforme spec-003.md
Estado inicial: Branch feature/selector criada, API /municipalities já existente

## Trabalho Realizado
- [x] Componente MunicipalitySelector.tsx criado
- [x] Integração com API via React Query
- [x] Loading state e error handling
- [x] Testes unitários para 5 cenários
- [ ] Integração com IndicatorTable (começado, 30% pronto)

## Decisões Importantes
- Usamos react-select ao invés de dropdown nativo (UX melhor para 139 itens)
- Cache de 5 minutos configurado no React Query

## Estado Atual
Funcionando:
- Busca e seleção de município
- Display de loading spinner
- Mensagem de erro se API falhar

Pendente:
- Callback onSelect não conectado ao componente pai
- Testes de integração com IndicatorTable

Bloqueios: Nenhum

## Próximos Passos
- [ ] Conectar MunicipalitySelector.onSelect ao state global
- [ ] Passar município selecionado para IndicatorTable
- [ ] Testar fluxo completo end-to-end

## Contexto para Próxima Sessão
O componente está isolado e funcionando. Próximo passo é integrá-lo
com o resto do dashboard. Ver docs/architecture/data-flow.md para
entender como estado flui entre componentes.
```

### ❌ Mau Handoff

```markdown
# Handoff

Fiz o seletor de município. Funciona. Próximo: integrar com tabela.
```

**Problemas**: Vago, sem contexto, sem decisões, sem estado claro.

## Checklist de Qualidade do Handoff

Um bom handoff deve responder:

- [ ] O que foi feito nesta sessão?
- [ ] Por que certas decisões foram tomadas?
- [ ] Qual é o estado atual do sistema?
- [ ] O que a próxima pessoa precisa fazer?
- [ ] Há bloqueios ou informações críticas?

**Próximo**: [ADR Protocol](./02_ADR_PROTOCOL.md)
