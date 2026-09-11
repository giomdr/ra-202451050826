# HANDOUT — AULA 03

## Consultoria de Design: a API da EscolaTech

*Identifique os anti-padrões e proponha o redesenho — Arquitetura de Aplicações Web*

## 🎯 MISSÃO

A EscolaTech contratou a consultoria de vocês para auditar a API do sistema escolar. Todos os endpoints abaixo FUNCIONAM e estão em produção — mas o time novo se recusa a mexer neles. Para CADA endpoint:

- Identifiquem o(s) problema(s) de design (pode haver mais de um!)
- Proponham o redesenho: método HTTP + rota + status codes corretos

*⏱️ Tempo: 25 minutos  |  👥 Formato: em duplas  |  Dica: se a rota conta o que faz em português, algo está errado.*

> **Nomes:** Giovanna Santos Madeira  **Turma:** ADS **Data:** 10/ 09 / 2026

## ENDPOINT 01 — POST /api/getAlunos

**Documentação atual (extraída da wiki da EscolaTech):**

```text
POST /api/getAlunos
Retorna TODOS os alunos cadastrados (hoje: 12.482 registros).
Resposta: 200 OK + array JSON completo (~9 MB).
Obs. da wiki: "usar POST porque GET não estava funcionando".
```

1. Qual(is) problema(s) de design vocês identificam?

Utiliza POST para consulta, sendo correto usar GET;
A rota descreve uma ação, em uma API REST a rota deve representar o recurso;
Retornar 12482 alunos não é interessante, uma boa prática seria usar paginação.

2. Seu redesenho (método + rota + status codes):

Usaria Get na rota /api/alunos, retornando 200 ok. Para muitos alunos o ideal é usar paginação.

## ENDPOINT 02 — GET /deletarAluno?id=7

**Documentação atual (extraída da wiki da EscolaTech):**

```text
GET /deletarAluno?id=7
Remove o aluno do banco de dados.
Resposta: 200 OK + "OK" (mesmo se o aluno não existir).
Obs. da wiki: "dá pra deletar pelo navegador, bem prático".
```

1. Qual(is) problema(s) de design vocês identificam?

Usar o GET para uma operação que altera o servidor (exclusão);
Usar uma rota que descreve uma ação(deletarAluno);
Retornar 200 mesmo quando o aluno não existe.

2. Seu redesenho (método + rota + status codes):

Usar DELETE na rota /api/aluno/7, retornar 204 no content quando o aluno fosse excluído e 404 Not Found se o aluno não existir.

## ENDPOINT 03 — POST /api/alunos (criação)

**Documentação atual (extraída da wiki da EscolaTech):**

```text
POST /api/alunos
Body: { "nome": "...", "curso": "..." }
Cria o aluno e responde: 200 OK + body "OK".
O app precisa buscar a lista inteira de novo para descobrir o ID gerado.
```

1. Qual(is) problema(s) de design vocês identificam?

Retonar 200 OK invés de 201 Created;
Não informar diretamente o ID do aluno criado;
Obrigar o app a buscar a lista inteira novamente.

2. Seu redesenho (método + rota + status codes):

Usar POST na rota /api/alunos re retornar 201 Created, com o aluno criado no corpo da resposta e o header location indicando a rota do novo aluno.

## ENDPOINT 04 — GET /escolas/1/turmas/3/alunos/25/matriculas/88/disciplinas/12

**Documentação atual (extraída da wiki da EscolaTech):**

```text
GET /escolas/1/turmas/3/alunos/25/matriculas/88/disciplinas/12
Retorna os dados da disciplina 12 da matrícula 88.
Para montar a URL o app precisa conhecer 5 IDs diferentes.
Resposta: 200 OK + JSON da disciplina.
```

1. Qual(is) problema(s) de design vocês identificam?

URL muito longa e complexa;
Vários níveis de recursos desnecessários.

2. Seu redesenho (método + rota + status codes):

Usar GET na rota e retornar 200 OK com os dados da disciplina.

## ENDPOINT 05 — GET /api/alunos/7/matriculas (erro)

**Documentação atual (extraída da wiki da EscolaTech):**

```text
GET /api/alunos/7/matriculas
Se o aluno 7 não existe, responde:
200 OK + "<html><b>Erro: aluno nao existe!</b></html>"
O app mobile quebra tentando fazer parse do JSON.
```

1. Qual(is) problema(s) de design vocês identificam?

Retornar 200 ok mesmo quando o aluno não existe;
Retornar HTML ao invés do JSON.

2. Seu redesenho (método + rota + status codes):

Materia o método GET na rota /api/alunos/7/matriculas. Se o aluno existir retornar 200 OK com JSON, se o aluno não existir retornar 404 Not Found com uma mensagem de erro JSON.

## ENDPOINT 06 — PUT /api/atualizarNotaParcial?aluno=7&disc=12&nota=8.5

**Documentação atual (extraída da wiki da EscolaTech):**

```text
PUT /api/atualizarNotaParcial?aluno=7&disc=12&nota=8.5
Atualiza SÓ a nota parcial da disciplina, sem body.
Todos os dados vão na query string.
Resposta: 200 OK + "OK".
```

1. Qual(is) problema(s) de design vocês identificam?

Utilizar PUT para uma atualização parcial;

Colocar os dados da alteração na query string em vez do body e usar uma rota que descreve ação.

2. Seu redesenho (método + rota + status codes):

Usar PATCH na rota /api/alunos/7/disciplinas/12 enviando uma nova nota no body. Retornaria 200 ok se a atualização tiver resposta ou 204 No Content se não houver conteúdo para retornar.

## DESAFIO

1. A EscolaTech quer lançar mudanças na API sem quebrar o app mobile antigo, que não recebe atualização há 2 anos. Que decisão de design — que falta na API INTEIRA — resolve esse problema? Como ficariam as rotas?

A decisão que falta na API é versionamento na API. Isso permite criar uma nova versão sem alterar ou quebrar a versão antiga. Assim o app antiga continua usando a V1, enquando novos aplicativos podem usar V2 com mudanças. 