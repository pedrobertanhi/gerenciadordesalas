# Estados e Transições da Reserva

## Objetivo

Definir os estados da reserva, as transições permitidas, as condições necessárias e as transições que devem ser recusadas.

A máquina de estados utiliza negação por padrão: qualquer transição não declarada neste documento é inválida.

## Estados

| Estado | Significado | Estado final |
|---|---|---|
| `SOLICITADA` | Aguarda aprovação ou processamento | Não |
| `APROVADA` | Recursos validados e reservados | Não |
| `REJEITADA` | Responsável recusou a solicitação | Sim |
| `EM_USO` | Utilização dos recursos foi iniciada | Não |
| `CONCLUIDA` | Utilização terminou corretamente | Sim |
| `CANCELADA` | Reserva foi cancelada antes do uso | Sim |
| `NAO_COMPARECEU` | Uso não foi iniciado no período esperado | Sim |

## Fluxo principal

`SOLICITADA → APROVADA → EM_USO → CONCLUIDA`

## Transições permitidas

| Origem | Destino | Responsável pela ação | Condições |
|---|---|---|---|
| `SOLICITADA` | `APROVADA` | Responsável ou Sistema | Recursos disponíveis, ausência de manutenção e nenhuma sobreposição |
| `SOLICITADA` | `REJEITADA` | Responsável | Recurso restrito e justificativa registrada |
| `SOLICITADA` | `CANCELADA` | Solicitante proprietário | Reserva ainda não iniciada |
| `APROVADA` | `SOLICITADA` | Sistema | Alteração relevante exige nova aprovação |
| `APROVADA` | `EM_USO` | Responsável | Horário de início alcançado e recursos liberados |
| `APROVADA` | `CANCELADA` | Solicitante proprietário | Cancelamento realizado antes do início |
| `APROVADA` | `NAO_COMPARECEU` | Responsável | Horário de início ultrapassado e utilização não iniciada |
| `EM_USO` | `CONCLUIDA` | Responsável | Utilização encerrada e materiais devolvidos |

## Aprovação de recursos

- reservas com recursos restritos permanecem em `SOLICITADA` até a decisão do Responsável;
- reservas sem recursos restritos podem passar automaticamente de `SOLICITADA` para `APROVADA`;
- a aprovação deve validar novamente disponibilidade, manutenção e sobreposição;
- solicitações concorrentes podem produzir somente uma aprovação;
- rejeições devem possuir justificativa;
- uma reserva rejeitada não pode ser reaberta; o Solicitante deverá criar outra solicitação.

## Alterações em reservas

O Solicitante pode alterar somente uma reserva própria que ainda não tenha iniciado.

Devem ser validados novamente:

- início e término;
- sala;
- professor;
- materiais e quantidades;
- manutenção;
- sobreposição;
- necessidade de aprovação.

Quando uma reserva `APROVADA` passar a utilizar um recurso restrito diferente ou tiver o período alterado, ela retorna para `SOLICITADA`.

Alterações sem impacto na aprovação permanecem no estado atual, mas devem gerar auditoria.

## Cancelamento

- somente o Solicitante proprietário pode cancelar sua reserva;
- o cancelamento deve ocorrer antes do início da utilização;
- o cancelamento libera os recursos reservados;
- uma reserva cancelada permanece no histórico;
- cancelar não significa excluir;
- reservas em `EM_USO` não podem ser canceladas.

## Início e conclusão

- somente uma reserva `APROVADA` pode entrar em `EM_USO`;
- o início registra autor e instante;
- uma reserva em uso não pode ser apagada;
- a conclusão exige que não existam materiais pendentes de devolução;
- retirada ou devolução parcial permanece registrada como movimentação;
- toda mudança de estado gera um evento de auditoria.

## Não comparecimento

Uma reserva `APROVADA` pode passar para `NAO_COMPARECEU` quando:

- o horário previsto já começou;
- a utilização não foi iniciada;
- um Responsável confirmou a ocorrência.

O estado `NAO_COMPARECEU` libera os recursos e encerra a reserva sem apagar seu histórico.

## Transições inválidas identificadas

| Origem | Destino inválido | Motivo |
|---|---|---|
| `SOLICITADA` | `EM_USO` | Não pode iniciar sem aprovação |
| `SOLICITADA` | `CONCLUIDA` | Não houve aprovação nem utilização |
| `SOLICITADA` | `NAO_COMPARECEU` | O não comparecimento exige reserva aprovada |
| `APROVADA` | `REJEITADA` | Após aprovação, deve ser cancelada antes do início |
| `APROVADA` | `CONCLUIDA` | Não pode concluir sem passar por `EM_USO` |
| `EM_USO` | `SOLICITADA` | A utilização já começou |
| `EM_USO` | `APROVADA` | Não é permitido retornar ao estado anterior |
| `EM_USO` | `REJEITADA` | A reserva já foi aprovada e iniciada |
| `EM_USO` | `CANCELADA` | Reserva iniciada não pode ser cancelada |
| `EM_USO` | `NAO_COMPARECEU` | A utilização já foi iniciada |
| `REJEITADA` | qualquer estado | Estado final |
| `CONCLUIDA` | qualquer estado | Estado final |
| `CANCELADA` | qualquer estado | Estado final |
| `NAO_COMPARECEU` | qualquer estado | Estado final |

## Regras de consistência

- o término deve ser posterior ao início;
- intervalos adjacentes são permitidos;
- sobreposição real é proibida;
- manutenção impede aprovação no período afetado;
- toda transição valida estado atual e perfil autorizado;
- toda transição registra estado anterior, novo estado, autor, instante e origem;
- repetir uma solicitação já concluída não pode criar outra transição;
- transições concorrentes devem ser protegidas por controle transacional;
- mensagens de erro não devem expor detalhes internos.
