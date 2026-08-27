# Entidades de Suporte

## Objetivo

Documentar as entidades que apoiam reservas, manutenção, movimentação de materiais, auditoria e notificações.

Este documento complementa o `MODELO_DE_DOMINIO.md` e atende principalmente aos requisitos RF-08 a RF-11.

## ReservationMaterial

Representa um material e sua quantidade dentro de uma reserva.

### Atributos

- `id`: identificador único;
- `reservation`: reserva relacionada;
- `material`: material solicitado;
- `quantity`: quantidade reservada.

### Regras

- a quantidade deve ser maior que zero;
- a combinação entre reserva e material deve ser única;
- a quantidade não pode superar a disponibilidade no período;
- alterações devem passar novamente pela validação de disponibilidade;
- o registro não pode existir sem reserva e material relacionados.

## Maintenance

Representa um período em que uma sala ou material permanece indisponível.

### Atributos

- `id`: identificador único;
- `room`: sala afetada, quando aplicável;
- `material`: material afetado, quando aplicável;
- `startAt`: início da manutenção;
- `endAt`: término da manutenção;
- `reason`: motivo;
- `status`: situação da manutenção;
- `createdBy`: Administrador responsável pelo registro;
- `createdAt`: instante da criação;
- `updatedAt`: instante da última alteração.

### Estados

- `AGENDADA`;
- `EM_ANDAMENTO`;
- `CONCLUIDA`;
- `CANCELADA`.

### Regras

- cada manutenção deve afetar exatamente uma sala ou um material;
- o término deve ser posterior ao início;
- o período bloqueado não pode aceitar novas reservas;
- conflitos com reservas existentes devem ser identificados antes da confirmação;
- somente o Administrador pode criar, alterar ou cancelar uma manutenção;
- uma manutenção concluída permanece no histórico.

## MaterialMovement

Representa a retirada ou devolução de materiais de uma reserva.

### Atributos

- `id`: identificador único;
- `reservation`: reserva relacionada;
- `material`: material movimentado;
- `type`: tipo da movimentação;
- `quantity`: quantidade movimentada;
- `performedBy`: Responsável que registrou a operação;
- `occurredAt`: instante da movimentação;
- `note`: observação opcional.

### Tipos

- `RETIRADA`;
- `DEVOLUCAO`.

### Regras

- a quantidade deve ser maior que zero;
- somente materiais pertencentes à reserva podem ser movimentados;
- a retirada acumulada não pode superar a quantidade reservada;
- a devolução acumulada não pode superar a quantidade retirada;
- retiradas e devoluções parciais são permitidas;
- somente o Responsável pode registrar movimentações;
- movimentações confirmadas não devem ser apagadas.

## AuditEvent

Representa o registro permanente de uma ação relevante.

### Atributos

- `id`: identificador único;
- `actor`: usuário que realizou a ação, quando existir;
- `action`: ação realizada;
- `entityType`: tipo da entidade afetada;
- `entityId`: identificador da entidade afetada;
- `previousState`: estado anterior, quando aplicável;
- `newState`: novo estado, quando aplicável;
- `origin`: origem da ação;
- `occurredAt`: instante do evento;
- `correlationId`: identificador utilizado para rastrear a operação.

### Origens

- `WEB`;
- `API`;
- `SISTEMA`.

### Regras

- toda mudança de estado da reserva deve gerar auditoria;
- alterações sensíveis de usuários, recursos, manutenções e movimentações devem ser auditadas;
- eventos de auditoria são somente de inclusão;
- registros não podem ser alterados ou excluídos pela aplicação;
- senha, token, credencial e conteúdo externo sensível não podem ser registrados;
- ações automáticas utilizam a origem `SISTEMA`.

## Notification

Representa uma tentativa de comunicação gerada por um evento do sistema.

### Atributos

- `id`: identificador único;
- `reservation`: reserva relacionada, quando aplicável;
- `recipient`: usuário destinatário;
- `eventType`: evento que originou a notificação;
- `channel`: canal utilizado;
- `status`: situação do envio;
- `attempts`: quantidade de tentativas;
- `externalReference`: referência devolvida pelo provedor, quando existir;
- `safeErrorCode`: código de erro sem informação sensível;
- `createdAt`: instante da criação;
- `sentAt`: instante do envio bem-sucedido, quando ocorrer.

### Canais

- `SIMULADO`;
- `HTTP_EXTERNO`.

### Estados

- `PENDENTE`;
- `ENVIADA`;
- `FALHOU`.

### Regras

- uma notificação deve estar relacionada a um evento válido;
- falha de notificação não desfaz a operação principal já confirmada;
- tentativas e falhas devem permanecer rastreáveis;
- erros não podem armazenar credenciais, tokens ou corpos externos sensíveis;
- o canal externo somente pode ser utilizado quando estiver configurado;
- na ausência de integração externa, o canal simulado continua disponível.

## Relacionamentos

| Origem | Relação | Destino | Cardinalidade |
|---|---|---|---|
| Reservation | possui | ReservationMaterial | um para muitos |
| Material | participa de | ReservationMaterial | um para muitos |
| Room | pode receber | Maintenance | um para muitos |
| Material | pode receber | Maintenance | um para muitos |
| User | cria | Maintenance | um para muitos |
| Reservation | possui | MaterialMovement | um para muitos |
| Material | participa de | MaterialMovement | um para muitos |
| User | registra | MaterialMovement | um para muitos |
| AuditEvent | possui como autor | User | muitos para zero ou um |
| Reservation | pode originar | Notification | um para muitos |
| User | recebe | Notification | um para muitos |

## Restrições gerais

- entidades históricas não devem ser removidas fisicamente;
- operações sensíveis devem registrar autor e instante;
- referências obrigatórias não podem ficar vazias;
- intervalos seguem o formato semiaberto `[início, término)`;
- datas persistidas devem representar instantes;
- autorização deve ser validada no serviço, independentemente da interface utilizada.
