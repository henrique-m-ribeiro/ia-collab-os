# Quickstart: Seu Primeiro Projeto com IA Collab OS

Este guia é um tutorial prático para iniciar seu primeiro projeto utilizando o framework IA Collab OS. Em cerca de 30 minutos, você terá configurado seu projeto e conduzido sua primeira sessão de planejamento, resultando no seu primeiro handoff para uma IA.

---

## Objetivo

Ao final deste guia, você terá:

- ✅ Criado a estrutura de pastas do framework no seu próprio repositório.
- ✅ Realizado a primeira sessão de trabalho no papel de **CEO (Humano)**.
- ✅ Produzido o **Plano do Sprint 1** para o seu projeto.
- ✅ Criado o seu primeiro **Handoff** para delegar a fase de especificação a uma **IA (CTO)**.

---

## Passo 0: Pré-requisitos

Antes de começar, garanta que você tem:

1.  **Uma ideia de projeto:** O que você quer construir? (Ex: "Um blog pessoal", "Um sistema de gestão de tarefas").
2.  **Acesso a um modelo de IA:** Qualquer modelo de linguagem avançado (como Claude, GPT-4, Gemini) pode atuar como CTO ou Dev.
3.  **Uma conta no GitHub:** Para criar o repositório do seu projeto.

---

## Passo 1: Configure seu Repositório (5 minutos)

O IA Collab OS funciona melhor em um ambiente versionado. Vamos criar a estrutura.

1.  **Crie um novo repositório no GitHub:** Dê um nome relacionado ao seu projeto (ex: `meu-blog-pessoal`).

2.  **Clone o repositório** para a sua máquina local.

3.  **Crie a estrutura de pastas de documentação.** Dentro do seu repositório, crie a seguinte estrutura:

    ```
    / (raiz do projeto)
    └── docs/
        ├── architecture/
        ├── decisions/
        ├── handoffs/
        └── sprints/
    ```

4.  **(Opcional) Copie os templates:** Para facilitar, copie os ficheiros do diretório `/templates` deste repositório para uma pasta `docs/templates` no seu projeto.

---

## Passo 2: Sua Primeira Sessão - Planejamento do Sprint (15 minutos)

Agora, você atuará como **CEO**. Seu objetivo é definir **o quê** e **porquê** construir no primeiro sprint.

1.  **Crie o seu primeiro artefacto:** Dentro da pasta `docs/sprints/`, crie um novo ficheiro chamado `sprint-1-plan.md`.

2.  **Preencha o plano do sprint.** Use o template abaixo como guia. Para este exemplo, vamos imaginar que nosso projeto é um "Blog Pessoal".

    ```markdown
    # Plano do Sprint 1: Fundação e Publicação do Primeiro Post

    ## Objetivo do Sprint
    > Ter um blog funcional, online, onde eu possa escrever e publicar o meu primeiro artigo utilizando Markdown.

    ## User Stories Priorizadas
    1.  **Como autor,** quero escrever posts em Markdown para focar no conteúdo.
    2.  **Como autor,** quero ver uma lista de todos os posts que já publiquei.
    3.  **Como leitor,** quero ler um post completo com formatação clara e legível.

    ## Critérios de Aceitação (O que significa "Pronto")
    - [ ] Consigo escrever um ficheiro `.md` e ele aparece como um post no site.
    - [ ] A página inicial lista todos os posts publicados, com título e data.
    - [ ] A página de um post mostra o conteúdo completo do Markdown renderizado como HTML.
    - [ ] O site está acessível publicamente através de uma URL.

    ## Fora do Escopo (Para Sprints Futuros)
    - Comentários nos posts.
    - Sistema de busca.
    - Paginação.
    - Autenticação de utilizador.
    ```

3.  **Faça o commit** deste ficheiro. Mensagem de commit sugerida: `docs: define plan for sprint 1`.

**Parabéns!** Você acabou de concluir a Fase 1 (Planejamento) da metodologia.

---

## Passo 3: Seu Primeiro Handoff - CEO para CTO (10 minutos)

Seu plano está pronto. Agora, você precisa delegar a tarefa de desenhar a arquitetura técnica para uma IA, que atuará como **CTO**.

1.  **Crie o ficheiro de handoff:** Dentro da pasta `docs/handoffs/`, crie um novo ficheiro chamado `2026-01-15_ceo_to_cto_sprint1_spec.md` (ajuste a data).

2.  **Preencha o handoff.** O objetivo é dar todo o contexto necessário para que a IA possa começar a trabalhar sem precisar de mais nada. Use o template abaixo:

    ```markdown
    # Handoff: CEO → CTO

    **Data:** 2026-01-15
    **Sessão de origem:** Planejamento do Sprint 1

    ## 1. Contexto

    Acabamos de definir o plano para o primeiro sprint do nosso projeto de "Blog Pessoal". O objetivo é ter um MVP funcional o mais rápido possível, focando na experiência de escrita e leitura.

    O plano completo está documentado em `docs/sprints/sprint-1-plan.md`.

    ## 2. Objetivo para o Destinatário (CTO)

    **Analisar o plano do Sprint 1 e criar uma especificação técnica detalhada para a sua implementação.**

    ## 3. Entregáveis Esperados

    | Entregável | Formato | Critério de Aceite |
    | :--- | :--- | :--- |
    | **Especificação Técnica** | Documento Markdown | - Define a stack tecnológica (linguagem, framework, etc.).<br>- Apresenta a arquitetura (ex: como os ficheiros `.md` serão lidos e transformados em páginas).<br>- Lista as principais decisões a serem tomadas (ADRs).<br>- Fornece um plano de implementação passo-a-passo para o Dev. |

    ## 4. Restrições e Decisões Já Tomadas

    - **Restrição:** A solução deve ser de baixo custo ou gratuita para hospedar (ex: Vercel, Netlify, GitHub Pages).
    - **Decisão:** O formato de escrita dos posts será obrigatoriamente Markdown.

    ## 5. Arquivos Relevantes

    | Arquivo | Motivo |
    | :--- | :--- |
    | `docs/sprints/sprint-1-plan.md` | **Fonte da Verdade:** Contém os requisitos e critérios de aceite para o sprint. |

    ## 6. Perguntas em Aberto

    - Qual a melhor forma de armazenar os ficheiros Markdown? (No próprio repositório Git? Num CMS headless?)
    - Qual framework (ex: Next.js, Astro, Eleventy) oferece o melhor balanço entre simplicidade e performance para este caso de uso?

    ## 7. Próximos Passos Sugeridos

    1.  Analisar as perguntas em aberto e propor uma solução, justificando os trade-offs.
    2.  Elaborar o documento de especificação técnica.
    3.  Criar um novo handoff (CTO para Dev) com as instruções para a implementação.
    ```

3.  **Faça o commit** deste ficheiro. Mensagem de commit sugerida: `docs: create handoff for sprint 1 specification`.

---

## Próximos Passos

Você está pronto! Sua próxima ação é iniciar uma nova sessão com a sua IA de escolha, dar a ela o papel de **CTO** e fornecer o conteúdo do seu ficheiro de handoff como o prompt inicial.

**Exemplo de prompt para a IA (CTO):**

> "Você é o CTO do nosso novo projeto. Sua primeira tarefa é analisar este handoff e produzir os entregáveis solicitados. Comece por analisar as perguntas em aberto e proponha uma solução. [Cole aqui o conteúdo completo do seu ficheiro de handoff]"

Bem-vindo ao IA Collab OS!
