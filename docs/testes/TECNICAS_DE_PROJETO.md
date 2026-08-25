# Técnicas de Projeto de Teste

## Objetivo

Explicitar como os casos são derivados para evitar tanto requisito não testado quanto ramo executado sem oráculo.

## Caixa-preta

- **Partição de equivalência:** recurso ativo/inativo/em manutenção; perfil válido/inválido; material suficiente/insuficiente.
- **Valor limite:** capacidade e quantidade em 0/1; início, fim e adjacência; antes/no/depois do bloqueio.
- **Tabela de decisão:** perfil × propriedade × ação; estado × evento; restrito × aprovador; manutenção × interseção.
- **Contrato:** status, código de erro, campos presentes e campos obrigatoriamente ausentes.

## Caixa-branca

- Medir linhas e branches com JaCoCo.
- Inspecionar cada condição composta das regras de conflito e autorização.
- Cobrir verdadeiro e falso de decisões relevantes.
- Não considerar cobertura como prova sem assertion de resultado/estado/interação essencial.
- Alterar ou remover uma regra em branch de trabalho para confirmar que o teste fica vermelho.

## Caixa-cinza

Testes de API usam apenas o contrato HTTP durante a execução, mas os casos são escolhidos com conhecimento dos riscos internos: transações, constraints, autorização, serialização e auditoria.

## Matriz mínima por risco

| Regra | Equivalência | Limite | Decisão | Branch/condição | Nível principal |
|---|---:|---:|---:|---:|---|
| término > início | sim | sim | — | sim | unidade/API |
| sobreposição | sim | sim | sim | sim | unidade/integração |
| quantidade de material | sim | sim | sim | sim | integração |
| autorização | sim | — | sim | sim | unidade/API |
| estados | — | — | sim | sim | unidade |
| manutenção | sim | sim | sim | sim | integração/API |
| concorrência | — | — | — | — | integração real |
| integração externa | sim | limite de timeout | sim | sim | WireMock |

## Regras da suíte

1. Escolher o nível mais baixo que captura o risco sem perder o comportamento relevante.
2. Evitar duplicar o mesmo oráculo em todos os níveis.
3. Testes unitários não usam internet, banco, relógio real, ordem global nem `Thread.sleep()`.
4. Tempo é injetado com `Clock` ou instante explícito.
5. Mocks ficam em unidades isoladas; PostgreSQL real usa Testcontainers; HTTP externo usa WireMock.
6. Cada teste possui uma razão principal para falhar e nome que comunica contexto, ação e resultado.
7. Casos, automação e execução recebem IDs/links na RTM.
