# Revisão formal do GOV-01

## Objetivo

Registrar o procedimento de revisão dos requisitos e das decisões arquiteturais antes de alterar os ADRs de `Proposta` para `Aceita`.

## Escopo da revisão

Cada integrante deverá conferir:

- os requisitos funcionais RF01 a RF14 no PRD;
- a ausência de funcionalidades fora do escopo;
- as decisões e consequências dos ADRs 001 a 007;
- as pendências registradas no índice de ADRs;
- a coerência entre arquitetura, segurança, concorrência e notificações.

## Documentos obrigatórios

- [ADR-001 — Stack tecnológica](0001-stack-tecnologica.md);
- [ADR-002 — Interface web e OpenAPI](0002-interface-web-e-openapi.md);
- [ADR-003 — Tempo e sobreposição](0003-tempo-e-sobreposicao.md);
- [ADR-004 — Arquitetura em camadas](0004-arquitetura-em-camadas.md);
- [ADR-005 — Autenticação e autorização](0005-autenticacao-e-autorizacao.md);
- [ADR-006 — Controle concorrente](0006-controle-concorrente-de-reservas.md);
- [ADR-007 — Estratégia de notificações](0007-estrategia-de-notificacoes.md).

## Evidência válida

Cada um dos três integrantes que não abriu o Pull Request deverá:

1. abrir a aba **Files changed**;
2. conferir este protocolo e os documentos vinculados;
3. selecionar **Review changes**;
4. registrar uma observação sobre o que foi verificado;
5. selecionar **Approve**;
6. enviar a revisão.

Comentários isolados e checkboxes marcados não substituem a aprovação formal do GitHub.

## Regra de estado

Os ADRs permanecerão como `Proposta` durante esta revisão.

Somente depois das três aprovações formais será criado um Pull Request separado. Esse Pull Request final deverá receber novas aprovações formais de Enzo, Matheus e Davis antes de alterar os sete ADRs e o índice para `Aceita`, além de preservar os commits e não utilizar squash.
