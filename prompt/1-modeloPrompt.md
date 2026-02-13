### Estrutura do Modelo de Prompt

**1. Persona e Escopo**
Defina quem a IA é (o papel/especialidade) e o que ela **não** deve fazer de imediato. Isso alinha a expectativa técnica e reduz improvisos.
*   *Exemplo:* "Você é um assistente especializado em Node.js versão X e testes com Jest. Não faça refatorações no código original".

**2. Objetivo**
Descreva de forma direta e sem ambiguidade o que precisa ser feito. Evite pedidos vagos como "escreva testes"; seja específico.
*   *Exemplo:* "Escreva testes unitários para a função abaixo usando o Jest, cobrindo casos de entradas válidos e inválidos".

**3. Entrada (Input)**
Forneça os dados ou código que serão processados. É crucial manter uma **separação visual** clara entre a instrução e a entrada, e listar apenas o necessário para a tarefa.
*   *Exemplo:* Colocar a função alvo separada do resto do texto.

**4. Formato de Saída**
Defina o formato exato da resposta (como JSON, Markdown, lista). Isso é essencial para mitigar riscos e permitir que a resposta seja usada por outros sistemas.
*   *Exemplo:* "Responda apenas com um objeto JSON no formato { test_file: ... }".

**5. Critérios de Qualidade**
Especifique o que define uma resposta "boa" para você. São as regras de sucesso da tarefa.
*   *Exemplo:* "O teste deve rodar com `npm test` sem ajustes; não usar bibliotecas externas além do Jest".

**6. Tratamento de Ambiguidades (Assumptions)**
Instrua a IA sobre como agir se faltar informação. Peça para ela listar quais pressupostos assumiu.
*   *Exemplo:* "Se faltar a versão do Node, assuma que é a versão 18. Liste todos os pressupostos feitos no campo `assumptions`".

**7. Instruções Negativas**
Liste explicitamente o que **não** pode aparecer na resposta final. Isso limpa a saída ("sujeira" como explicações extras).
*   *Exemplo:* "Não inclua explicações ou comentários fora do JSON".

**8. Tratamento de Erro**
Defina como a IA deve responder caso não consiga cumprir a tarefa.
*   *Exemplo:* "Se não for possível cumprir, retorne um JSON com `status: error` e a explicação do motivo".

### "Regras de Ouro" para Revisão
Antes de rodar o prompt, o material sugere um checklist mental (Regras de Ouro) para verificar se você definiu:
1.  Persona/Escopo
2.  Objetivo direto
3.  Entradas separadas e mínimas
4.  Formato de saída
5.  Critérios claros
6.  Tratamento de ambiguidade/erros
7.  Proibições necessárias.

Essa estrutura ajuda a transformar o prompt em um componente reutilizável e sustentável ao longo do tempo.