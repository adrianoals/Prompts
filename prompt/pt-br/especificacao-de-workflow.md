# Relatório Completo do Estado do Projeto

## Descrição

Produza um snapshot completo e auditável do projeto coordenando agentes especialistas e consolidando suas saídas. Claude Code (VOCÊ) atua como coordenador. O agente orquestrador apenas prepara a estrutura e depois sintetiza as saídas. A entrega final é:

1. Um arquivo README chamado `README-YYYY-MM-DD-HH:MM:SS.md` com o timestamp **ATUAL**, colocado dentro do diretório do agente orquestrador, contendo uma breve descrição do projeto e um índice com link para cada relatório produzido pelos agentes

Ao criar o índice do README, liste apenas o título de cada relatório e faça link direto para o arquivo usando caminho absoluto que começa na raiz do repositório com uma única barra inicial. Exemplo de formato de link:

```md
[Arquitetura do Projeto](/<link-para-o-relatorio>)
```

Valide cada link antes de salvar o README e garanta que o caminho realmente exista. Use o `MANIFEST.md` gerenciado pelo agente orquestrador como fonte da verdade para mapear todos os relatórios produzidos. Ele é **INICIALIZADO** na Fase 1, **ATUALIZADO** após cada agente concluir nas Fases 2 e 3, e **FINALIZADO** na Fase 4. Certifique-se de informar esse comportamento ao agente Orquestrador.

---

## Template de saída

Use o template a seguir para o arquivo README. Substitua placeholders e remova seções vazias. Não insira linhas horizontais.

```md
# <Nome do Projeto> Project State Full Report

<Breve descrição do projeto em um ou dois parágrafos>.
<Breve descrição explicando os objetivos deste documento, que é a consolidação dos principais aspectos do projeto como um todo, como um raio-x.

Generated on: YYYY-MM-DD HH:MM:SS

## Overview and Architecture
<Project Overview>
<Project Architecture>

## Components
<Component Name>

## Dependencies
<Dependencies Report>
```

---

## Restrições Críticas

1. NUNCA repasse o workflow completo ao agente orquestrador
2. O agente orquestrador DEVE executar APENAS duas responsabilidades.
   a) Fase 1 cria a estrutura do projeto conforme a especificação do agente ou flags explícitas do usuário, como `--project-folder`, `--output-folder` e `--ignore-folders`
   b) Fase 4 sintetiza as saídas geradas por outros agentes
3. Mantenha SEPARAÇÃO ESTRITA entre agentes
   a) Invoque cada agente especialista em uma chamada Task separada
   b) O agente orquestrador não pode spawnar subagentes
   c) Claude Code (VOCÊ) é o coordenador que sequencia fases e execuções paralelas
4. SIGA EXATAMENTE a especificação escrita de cada agente armazenada em `.claude/agents`
5. NUNCA escreva saídas fora dos locais designados. NUNCA crie pastas que não estejam explicitamente definidas pelo orquestrador ou pelas especificações dos agentes
6. NUNCA forneça recomendações, planos de ação, mudanças de código ou instruções de upgrade no Project Overview. Resuma apenas o que os agentes reportaram
7. NÃO estime tempos ou durações. EVITE linguagem vaga como provavelmente seguro ou deve estar ok
8. NÃO invente CVEs ou vulnerabilidades. Use evidências produzidas pelo dependency-auditor

---

## SEPARAÇÃO OBRIGATÓRIA DE AGENTES

* Cada agente DEVE ser invocado com uma chamada separada da ferramenta Task
* O orquestrador NÃO DEVE ser solicitado a criar subagentes
* VOCÊ (Claude Code) é o coordenador, NÃO o agente orquestrador
* Toda comunicação flui por VOCÊ como coordenador. VOCÊ decide quais novos agentes iniciar. Após qualquer agente finalizar, VOCÊ DEVE acionar o orquestrador para atualizar o `MANIFEST.md`. Na prática, isso significa chamar `Task(orchestrator)` uma vez por tarefa de agente concluída para que ele acrescente a entrada (título, caminho absoluto enraizado em `/`, agente, timestamp). Exemplo: quando `Task(architectural-analyzer)` concluir, invoque imediatamente `Task(orchestrator)` para registrar o Architecture Report no `MANIFEST.md`.

---

## Fluxo de Execução

### Fase 1: Task(orchestrator)

1. Leia flags do usuário e normalize caminhos. Respeite `--project-folder` e `--output-folder` quando fornecidos. Se não forem fornecidos, use os locais padrão definidos pela especificação do orquestrador
2. Crie apenas os diretórios exigidos pela especificação do orquestrador. Não invente níveis extras como output ou reports, a menos que a própria especificação exija
3. Aplique lista de ignorados antes de qualquer leitura de arquivo. Nunca parseie ou escaneie arquivos em qualquer pasta listada em `--ignore-folders`
4. Inicialize `MANIFEST.md` no diretório do orquestrador com estrutura de índice vazia (título, caminho absoluto, agente, timestamp)

### Fase 2: Task(dependency-auditor) e Task(architectural-analyzer) em paralelo

1. Task(dependency-auditor) produz relatório completo de dependências seguindo sua especificação. Importante: para validação de dependências nesta Task, use servidores MCP como Context7 e Firecrawl para verificar versões, status de manutenção e vulnerabilidades conhecidas
2. Task(architectural-analyzer) produz relatório completo de Arquitetura seguindo sua especificação. Importante: SOMENTE o orquestrador acrescenta no `MANIFEST.md` quando cada Task concluir

### Fase 3: Task(component-deep-analyzer) em paralelo, um por componente

1. Parseie o Architecture Report da Fase 2, tratando-o como artefato produzido pelo architectural-analyzer
2. Para cada componente listado no Architecture Report (por exemplo, na seção "Critical Components Analysis" ou qualquer seção que enumere componentes), dispare uma Task(component-deep-analyzer) separada para aquele componente, em paralelo
3. Cada Task(component-deep-analyzer) DEVE analisar completamente apenas o componente designado e produzir um relatório individual
4. Requisito de cobertura e exemplo: se o Architecture Report listar 10 componentes, VOCÊ DEVE disparar 10 execuções paralelas de Task(component-deep-analyzer) e produzir 10 relatórios de componente correspondentes. Nenhum componente pode ser pulado
5. Após todas as execuções Task(component-deep-analyzer) concluírem, VOCÊ DEVE verificar que todo componente possui relatório correspondente. Reabra o Architecture Report e revise as seções de componente linha por linha. Se qualquer componente estiver sem relatório, dispare Tasks adicionais para os componentes faltantes até cobertura de 100%

IMPORTANTE: garanta que NÃO haja relatórios duplicados, então **ultrathink** para verificar se o relatório já existe antes de criar um novo com nome, timestamp etc. diferentes ou similares. VOCÊ DEVE ser extremamente preciso nessa verificação.

### Fase 4: Task(orchestrator)

1. Agregue referências para todos os relatórios gerados
2. FINALIZE o `MANIFEST.md` dentro do diretório do agente orquestrador: valide todas as entradas, garanta que títulos e caminhos absolutos existam, remova duplicações, confirme nomes de agentes e timestamps

### Fase 5: Task(VOCÊ)

1. Leia o `MANIFEST.md` do diretório do orquestrador. Monte o índice usando títulos dos relatórios e links absolutos iniciando com uma única barra
2. Valide cada link antes de escrever. Use o algoritmo de validação de link definido abaixo
3. Salve `README-YYYY-MM-DD-HH:MM:SS.md` com data/hora ATUAL dentro do diretório do orquestrador

LEMBRETE: o agente orquestrador é apenas mais um especialista que:

* Configura a estrutura do projeto (Fase 1)
* Sintetiza saídas (Fase 4)
* Ele NÃO coordena outros agentes — esse é o SEU trabalho (VOCÊ, Claude Code)

---

## Exemplos de Uso

Use `$ARGUMENTS` como pasta do projeto e, se fornecido, o caminho/nome de saída dos arquivos.

NUNCA use outros caminhos para salvar relatórios, arquivos ou manifests, a menos que sejam explicitamente fornecidos pelo usuário. Não crie subpastas como `reports` ou `output`.

NUNCA crie arquivos ou pastas que não estejam especificados na especificação do agente.

SIGA EXATAMENTE o padrão de saída definido abaixo.

```bash
# Rodar o workflow no projeto com base na pasta raiz
/run-project-state-full-report

# Rodar o workflow na pasta de projeto fornecida pelo usuário
/run-project-state-full-report --project-folder=project-folder

# Rodar o workflow e salvar relatórios na pasta fornecida pelo usuário. Todos os relatórios, arquivos e manifests devem ser salvos na pasta de saída usando o padrão: <output-folder>/<agent-name>/<file-name-provided-by-agent>.md. Exemplo: output-folder/dependency-auditor/dependencies-report-YYYY-MM-DD-HH:MM:SS.md
/run-project-state-full-report --project-folder=project-folder --output-folder=output-folder

# Rodar o workflow ignorando pastas fornecidas pelo usuário. Não ler nem auditar arquivos dessas pastas
/run-project-state-full-report --ignore-folders=adk_repo,venv,.env,node_modules,.git
```

---

## Instruções Negativas

1. NUNCA modifique ou sugira edições na base de código.

   * Exemplos: NÃO abrir pull requests, renomear arquivos, refatorar funções, mudar defaults de configuração ou alterar scripts de build.
   * Permitido: apenas resumir achados e referenciar o relatório específico do agente onde o problema está documentado.
2. NÃO execute upgrades nem prescreva migrações.

   * Exemplos proibidos: "npm update", "go get -u", "helm upgrade", "apply database migrations".
   * Este comando é descritivo, não prescritivo. Mantenha saídas informativas.
3. NÃO invente CVEs nem assuma vulnerabilidades sem evidência explícita do dependency-auditor ou validação MCP.

   * Proibido: "provavelmente vulnerável", "likely CVE-2023-XXXXXX" ou alegações de segurança não verificadas.
   * Permitido: citar nomes exatos de pacotes/versões e referenciar achados produzidos pelo dependency-auditor.
4. NÃO use linguagem vaga.

   * Evite frases como "provavelmente seguro", "deve estar ok", "parece estável".
   * Use linguagem neutra e factual extraída dos relatórios dos agentes.
5. NÃO use emojis ou caracteres estilizados.
6. NÃO forneça estimativas de tempo.

   * Proibido: "em 2 horas", "até amanhã", "em X dias" ou qualquer estimativa de duração.
   * Se timing for solicitado, informe que estimativas estão fora do escopo deste comando.
7. NUNCA crie pastas de agente na raiz do repositório.

   * Proibido: `/agents`, `/architectural-analyzer` na raiz do repositório.
   * Permitido: usar apenas caminhos especificados por cada agente ou pelo orquestrador, por exemplo `docs/agents/orchestrator`.
8. NUNCA crie arquivos ou pastas não especificados pelas especificações do agente ou do orquestrador.

   * Proibido: diretórios ad-hoc como `reports`, `output`, `tmp` a menos que explicitamente definidos.
   * Todas as saídas devem seguir `<output-folder>/<agent-name>/<file-name-provided-by-agent>.md` ou ser armazenadas no diretório do orquestrador conforme especificado.
9. NUNCA duplique um relatório. Se precisar ajustar algo, edite o relatório que já existe em vez de criar um novo.

   * Proibido: duplicar relatório de "component-analysis" durante revisão se todos já existirem.

---

## Observações

Ultrathink cada etapa do workflow e determine instruções claras para cada agente completar suas tarefas. Como coordenador/mestre (VOCÊ), você DEVE fornecer todo o contexto necessário para cada agente. Para isso, leia cada especificação de agente, entenda passo a passo o que cada agente precisa fazer e repasse os inputs, restrições e caminhos específicos necessários.

Apenas o orquestrador mantém o `MANIFEST.md`. Instrua o orquestrador a acrescentar uma nova entrada no `MANIFEST.md` imediatamente após qualquer agente finalizar, registrando título do relatório, caminho absoluto enraizado em `/`, nome do agente e timestamp.

Exemplo: assim que o Architecture Report estiver pronto, o orquestrador DEVE acrescentar sua entrada no `MANIFEST.md` e marcar aquela tarefa como concluída.

---

## Template de MANIFEST.md

```md
# MANIFEST — <Project Name>
Generated on: YYYY-MM-DD-HH:MM:SS
Orchestrator Path: /docs/agents/orchestrator

## Tracked Reports
- Project Architecture: /docs/agents/architectural-analyzer/<file-name>.md
- Components:
  - <Component Name>: /docs/agents/component-deep-analyzer/<component-name>-report-YYYY-MM-DD-HH:MM:SS.md
- Dependencies: /docs/agents/dependency-auditor/<file-name>.md

## Workflow
- Task IDs and timestamps for each reported artifact
- Status: completed | pending | failed
- Notes: minimal operational notes only

## General Information
- Project folder: <path>
- Output folder: <path>
- Ignore folders: <list>
```
