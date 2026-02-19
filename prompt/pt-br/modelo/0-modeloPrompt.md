# Estruturacao de prompts elaborados

_(normalmente nao serao executados em alta escala)_

## Persona e Escopo

- Defina quem o modelo "e" e o que NAO deve fazer.
- Reduz improvisos e garante alinhamento tecnico.

> "Voce e um assistente especializado em Node.js vX e testes com Jest.  
> Nao faca refatoracoes no codigo original."

## Objetivo

- Descreva de forma direta e sem ambiguidade o que precisa ser feito.

Escreva testes para o codigo abaixo.  
Escreva testes undtarios para a funcao abalixo usando Jest, cobrinda casos de entrada validos e invalidas.

## Entradas

Liste somente o necessario para resolver a tarefa.

- Mantenha separacao visual.
- Evite colar arquivos inteiros se so a parte a e relevante.

## Entradas

Liste somente o necessario para resolver a tarefa.

- Mantenha separacao visual.
- Evite colar arquivos inteiros se so a parte a e relevante.

### FUNCAO

```js
function calculateDiscount (price, percentage) {
  if (price <= 0 || percentage < 0) return 0;
  return price - (price * percentage / 100);
  }
```

## Formato de saida

Defina o formato exato para minimizar riscos de respostas fora de padrao.

Responda apenas com um objeto JSON no formato:

```json
{
  "testFile": "<conteudo do arquivo de teste>",
  "coverageNotes": "<breve descricao de cobertura>*"
}
```

## Criterios de qualidade

Especifique as regras que definem uma boa resposta.

Criterios:

1. O teste deve rodar com 'npm test' sem ajustes.
2. Deve cobrir casos de entrada validos e invalidos.
3. Nao usar bibliotecas externas alem do Jest.

## Tratamento de ambiguidades e assumptions

Diga o que fazer se faltar informacao.

- Se faltar a versao do Node, assuma v18.
- Liste todos os pressupostos feitos no campo "assumptions".

## Instrucoes negativas

Liste o que nao pode aparecer na resposta.

- Nao inclua explicacoes ou comentarios fora do JSON.

## Tratamento de erros

Explique como retornar se nao foi possivel cumprir.

Se nao for possivel atender aos criterios, retorne:

```json
{
  "status": "ERROR",
  "reason": "<explicacao do problema>"
}
```

## Exemplo completo

```
Persona & Escopo:
Voce e um assistente especializado em Node.js v18 e testes com Jest.
Nao faca refatoracoes no codigo original.

Objetivo:
Gerar testes unitarios para a funcao abaixo usando Jest.

Entrada:
function calculateDiscount(price, percentage) {
if (price <= @ || percentage < 0) return 0; return price - (price * percentage / 100);
}

Formato de saida:
Responda apenas com um objeto JSON no formato:
{
"testFile": "<conteudo do arquivo de teste>",
"coverageNotes": "<breve descricao de coberturo>",
"assumptions": []
}

Criterios:
1. O teste deve rodar com "npm test" sem ajustes.
2. Cobrir casos de entrada validos e invalidos.
3. Nao usar bibliotecas externas alem do Jest.

Ambiguidade & Pressupostos:
Se faltar versao do Node, assuma v18 e adicione em "assumptions".

Instrucoes Negativas:
Nao inclua explicacoes ou comentarios fora do ISON.

Tratamento de Erros:
Se nao puder cumprir os criterios, retorne:
{
"status": "ERROR",
"reason": "<explicacao>"
}
```
