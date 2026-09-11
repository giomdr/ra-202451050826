# HANDOUT — AULA 02

## Dissecando o HTTP

*6 requisições sob o microscópio — Arquitetura de Aplicações Web*

## 🎯 MISSÃO

Vocês interceptaram 6 conversas entre um app e a API de uma biblioteca. Para CADA card:

- Descrevam o que o cliente pediu (verbo + recurso na URI)
- Expliquem o que o status code da resposta informa
- Respondam: repetindo a MESMA requisição 3 vezes seguidas, o estado do servidor muda?

Ao final, preencham juntos a TABELA-SÍNTESE dos verbos na última página.

*⏱️ Tempo: 30 minutos  |  👥 Formato: em duplas  |  Dica: o card 6 esconde uma pegadinha de quem é a culpa.*

> **Nomes:** Giovanna Santos Madeira  **Turma:** ADS **Data:** 10 / 09 / 2026

## REQUISIÇÃO 01 — A prateleira inteira

```text
→ REQUISIÇÃO
GET /api/livros HTTP/1.1
Host: biblioteca.newton.br
Accept: application/json
```

```text
← RESPOSTA
HTTP/1.1 200 OK
Content-Type: application/json

[ { "id": 1, "titulo": "Clean Code", "autor": "Robert C. Martin" },
  { "id": 7, "titulo": "O Programador Pragmático", "autor": "Hunt & Thomas" } ]
```

**Sua análise:**

1. O que o cliente pediu (verbo + recurso)?

O cliente fez uma requisição ao servidor (Get), que retornará uma página web (biblioteca.newton.br)

2. O que o status code informa? Deu certo? Culpa de quem se não deu?

O status code informa o resultado, não houve erro então não existe culpado. Significa que o servidor recebeu o pedido e retornou corretamente.

3. Repetindo esta requisição 3 vezes seguidas, o estado do servidor muda? E a resposta?

O GET é usado apenas para consultar dados, então repetir não altera o estado do servidor. As respostas não mudam.

## REQUISIÇÃO 02 — O livro fantasma

```text
→ REQUISIÇÃO
GET /api/livros/99 HTTP/1.1
Host: biblioteca.newton.br
Accept: application/json
```

```text
← RESPOSTA
HTTP/1.1 404 Not Found
Content-Type: application/problem+json

{ "title": "Not Found", "status": 404 }
```

**Sua análise:**

1. O que o cliente pediu (verbo + recurso)?

Foi feita uma requisição (GET), o recurso foi /api/livros/99.

2. O que o status code informa? Deu certo? Culpa de quem se não deu?

A requisição chegou ao servidor mas o recurso não foi encontrado pois não existe o livro 99. Não existe culpado pois o livro não existe.

3. Repetindo esta requisição 3 vezes seguidas, o estado do servidor muda? E a resposta?

O estado não muda pois GET apenas consulta, a resposta será a mesma pois o livro permanece inexistente.

## REQUISIÇÃO 03 — Livro novo na estante

```text
→ REQUISIÇÃO
POST /api/livros HTTP/1.1
Host: biblioteca.newton.br
Content-Type: application/json

{ "titulo": "Domain-Driven Design", "autor": "Eric Evans" }
```

```text
← RESPOSTA
HTTP/1.1 201 Created
Location: /api/livros/8
Content-Type: application/json

{ "id": 8, "titulo": "Domain-Driven Design", "autor": "Eric Evans" }
```

**Sua análise:**

1. O que o cliente pediu (verbo + recurso)?

Verbo POTS, recurso /api/livros/8. Adicionar um novo livro a biblioteca.

2. O que o status code informa? Deu certo? Culpa de quem se não deu?

201 created, o livro foi criado com sucesso, não houve erro e nem culpado. 

3. Enviando este POST 3 vezes seguidas, o que acontece na estante? Para que serve o header Location?

Se enviar 3 vezes teremos 3 livros cadastrados, possivelmente com IDS diferentes. O header Location informa onde o novo recurso foi criado.

## REQUISIÇÃO 04 — Corrigindo a ficha completa

```text
→ REQUISIÇÃO
PUT /api/livros/7 HTTP/1.1
Host: biblioteca.newton.br
Content-Type: application/json

{ "id": 7, "titulo": "O Programador Pragmático", "autor": "D. Hunt; D. Thomas" }
```

```text
← RESPOSTA
HTTP/1.1 200 OK
Content-Type: application/json

{ "id": 7, "titulo": "O Programador Pragmático", "autor": "D. Hunt; D. Thomas" }
```

**Sua análise:**

1. O que o cliente pediu (verbo + recurso)?

Verbo PUT (atualizar), verbo /api/livros/7. Atualizar livro 7.

2. O que o status code informa? Deu certo? Culpa de quem se não deu?

200 OK. A atualização foi concluída sem erros, não existe culpado. 

3. Repetindo esta requisição 3 vezes seguidas, o estado do servidor muda? E a resposta?

Permanece igual após a primeira atualização. 

## REQUISIÇÃO 05 — Fora do catálogo

```text
→ REQUISIÇÃO
DELETE /api/livros/7 HTTP/1.1
Host: biblioteca.newton.br
```

```text
← RESPOSTA
HTTP/1.1 204 No Content
```

**Sua análise:**

1. O que o cliente pediu (verbo + recurso)?

Verbo DELETE (excluir), recurso /api/livros/7. Excluir o livro 7.

2. O que o status code informa? Deu certo? Culpa de quem se não deu?

204 No Content, o livro foi excluído. Não existe culpado.

3. Repetindo o DELETE, o estado do servidor muda? Que resposta você ESPERA na segunda vez?

Após a exclusão o estado não muda, pois a ação já foi executada. 

## REQUISIÇÃO 06 — O cadastro capenga

```text
→ REQUISIÇÃO
POST /api/livros HTTP/1.1
Host: biblioteca.newton.br
Content-Type: application/json

{ "autor": "Anônimo" }
```

```text
← RESPOSTA
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json

{ "title": "Bad Request", "status": 400,
  "errors": { "Titulo": [ "O campo Titulo é obrigatório" ] } }
```

**Sua análise:**

1. O que o cliente pediu (verbo + recurso)?

Verbo POST (criar), recurso /api/livros. Cadastrar um novo livro.

2. O que o status code informa? Deu certo? Culpa de quem se não deu?

 400 bad request, a requisição está inválida. O culpado neste caso é o cliente que enviou dados incompletos, faltou o título.

3. Repetindo esta requisição 3 vezes seguidas, o estado do servidor muda? E a resposta?

O estado do servidor não muda pois não é possível realizar a criação com dados obrigatórios incompletos. 

## TABELA-SÍNTESE — Os verbos do HTTP

*Preencham com base nos 6 cards. “Seguro” = não altera nada no servidor. “Idempotente” = repetir N vezes deixa o servidor no mesmo estado que 1 vez.*

| **Verbo** | **Para que serve** | **Seguro?** | **Idempotente?** | **Status típicos** |
| --- | --- | --- | --- | --- |
| **`GET`** | Busca | Sim | Sim | 200,404 |
| **`POST`** |  Criar um novo recurso| não |não  | 201,400 |
| **`PUT`** |  Atualizar|  não|sim  |200,404  |
| **`PATCH`** | Alterar parcialmente | não | geralmente | 200,404 |
| **`DELETE`** | Excluir | não | sim |  204,404|

## DESAFIO

1. O verbo PATCH não apareceu em nenhum card. Qual a diferença entre PATCH e PUT? Um app de banco quer alterar SÓ o apelido do usuário, entre dezenas de campos do perfil — qual dos dois você usaria e por quê?

PUT: Atualiza o recurso inteiro.
PATCH: Aletra apenas uma parte do recurso.

No caso do banco usaria PATCH, pois a alteração será em apenas um campo específico.