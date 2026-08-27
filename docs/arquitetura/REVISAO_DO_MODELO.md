# Revisão do Modelo de Domínio

## Objetivo

Registrar a revisão documental do modelo de domínio, dos estados e das permissões contra a especificação do projeto.

Esta revisão não representa execução de testes de código. A implementação e as evidências automatizadas permanecem pendentes.

## Documentos analisados

- [PRD](../PRD.md);
- [RTM](../RTM.md);
- [Modelo de domínio](MODELO_DE_DOMINIO.md);
- [Entidades de suporte](ENTIDADES_DE_SUPORTE.md);
- [Estados da reserva](ESTADOS_DA_RESERVA.md);
- [Diagrama do domínio](../diagramas/MODELO_DE_DOMINIO.md);
- [Matriz de permissões](../seguranca/MATRIZ_DE_PERMISSOES.md);
- ADR-003 sobre tempo e sobreposição;
- ADR-005 sobre autenticação e autorização;
- ADR-006 sobre concorrência;
- ADR-007 sobre notificações.

## Método

A revisão comparou:

1. entidades exigidas pela Issue #2;
2. relacionamentos necessários aos requisitos funcionais;
3. regras críticas RC-01 a RC-12;
4. transições válidas e inválidas;
5. ações sensíveis e perfis autorizados;
6. limitações que ainda dependem de implementação e testes.

## Entidades exigidas

| Entidade | Documento | Resultado |
|---|---|---|
| `User` | Modelo de domínio | Documentada |
| `Room` | Modelo de domínio | Documentada |
| `Professor` | Modelo de domínio | Documentada |
| `Material` | Modelo de domínio | Documentada |
| `Reservation` | Modelo de domínio | Documentada |
| `ReservationMaterial` | Entidades de suporte | Documentada |
| `Maintenance` | Entidades de suporte | Documentada |
| `MaterialMovement` | Entidades de suporte | Documentada |
| `AuditEvent` | Entidades de suporte | Documentada |
| `Notification` | Entidades de suporte | Documentada |

## Revisão das regras críticas

| Regra | Caso crítico revisado | Comportamento esperado | Resultado documental |
|---|---|---|---|
| RC-01 | Término igual ou anterior ao início | Recusar a reserva | Coberto |
| RC-02 | Reserva começa quando outra termina | Permitir horários adjacentes | Coberto |
| RC-03 | Duas reservas usam a mesma sala no mesmo período | Recusar a sobreposição | Coberto |
| RC-04 | Professor aparece em duas reservas simultâneas | Recusar a sobreposição | Coberto |
| RC-05 | Quantidade solicitada supera a disponibilidade | Recusar a reserva | Coberto |
| RC-06 | Duas solicitações concorrentes disputam o mesmo recurso | Aceitar no máximo uma | Coberto |
| RC-07 | Recurso possui manutenção no período | Recusar a reserva | Coberto |
| RC-08 | Reserva segue o fluxo principal | Permitir somente estados previstos | Coberto |
| RC-09 | Reserva é rejeitada, cancelada ou marcada como não comparecimento | Encerrar no estado alternativo correto | Coberto |
| RC-10 | Perfil não autorizado tenta aprovar recurso restrito | Negar a ação | Coberto |
| RC-11 | Tentativa de apagar reserva iniciada | Negar exclusão e preservar histórico | Coberto |
| RC-12 | Reserva muda de estado | Gerar evento de auditoria | Coberto |

## Revisão das transições inválidas

Foram identificadas explicitamente como inválidas:

- iniciar uma reserva ainda `SOLICITADA`;
- concluir sem passar por `EM_USO`;
- rejeitar uma reserva já aprovada;
- cancelar uma reserva em uso;
- marcar não comparecimento depois que o uso começou;
- retornar uma reserva em uso para estado anterior;
- alterar qualquer estado final;
- forçar transição não declarada.

O modelo utiliza negação por padrão para impedir outras combinações não previstas.

## Revisão da autorização

| Caso | Resultado esperado | Cobertura |
|---|---|---|
| Usuário inativo tenta autenticar | Negado | Matriz de permissões |
| Solicitante consulta reserva de outra pessoa | Negado | Propriedade obrigatória |
| Solicitante altera a própria reserva antes do uso | Permitido com condições | Perfil, propriedade e estado |
| Solicitante aprova recurso restrito | Negado | Somente Responsável |
| Responsável aprova recurso restrito disponível | Permitido com condições | Perfil e estado |
| Responsável administra usuários | Negado | Somente Administrador |
| Administrador mantém salas e materiais | Permitido | Perfil Administrador |
| Administrador força transição de reserva | Negado | Sem herança operacional |
| Usuário tenta apagar histórico | Negado | Exclusão física proibida |
| Sistema aprova recurso não restrito | Permitido internamente | Origem `SISTEMA` auditada |

## Relacionamentos verificados

- usuário cria reservas;
- professor e sala participam das reservas;
- materiais são ligados por `ReservationMaterial`;
- manutenção bloqueia sala ou material;
- movimentação registra retirada e devolução;
- auditoria registra ações humanas ou automáticas;
- notificações possuem destinatário e podem se relacionar com reservas.

As cardinalidades estão registradas no modelo textual e no diagrama Mermaid.

## Pontos pendentes

As seguintes atividades dependem de etapas futuras:

- implementar as entidades em Java e PostgreSQL;
- criar migrations;
- implementar a máquina de estados;
- centralizar a autorização nos serviços;
- criar testes automatizados para os casos revisados;
- executar testes concorrentes no banco;
- registrar evidências reais na RTM;
- definir e testar a política única entre `403` e `404`;
- obter revisão por pares no Pull Request.

## Resultado

A revisão documental confirmou que:

- as entidades exigidas pela Issue #2 estão documentadas;
- os relacionamentos necessários estão identificados;
- as transições inválidas estão registradas;
- as ações sensíveis possuem permissão ou proibição explícita;
- as regras críticas possuem comportamento esperado;
- nenhuma evidência de implementação foi declarada antes da existência do código.

O modelo está pronto para revisão da equipe, mas somente será considerado aprovado após o Pull Request e a manifestação formal dos revisores.