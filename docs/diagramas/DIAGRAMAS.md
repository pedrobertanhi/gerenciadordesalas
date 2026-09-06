# Documentação Arquitetural e de Dados (`[GOV-03]`)

## 1. Diagrama de Contexto (C4 - Nível 1)

```mermaid
C4Context
    title Diagrama de Contexto - Sistema de Organização de Recursos (Gerenciador de Salas)

    Person(solicitante, "Solicitante", "Professor/Coordenador: consulta disponibilidade e cria/altera/cancela reservas.")
    Person(responsavel, "Responsável", "Valida alocação de docentes, aprova recursos restritos e acompanha retiradas/devoluções.")
    Person(admin, "Administrador", "Gerencia salas, professores, materiais, usuários, bloqueios e períodos de manutenção.")

    System(sistema, "Gerenciador de Recursos API", "Sistema Spring Boot 3.x responsável pelas alocações, validações antirreserva dupla, ciclo de vida e auditoria.")

    SystemDb(db, "PostgreSQL 16", "Banco de dados relacional para persistência de dados de usuários, recursos, reservas e histórico auditável.")
    System_Ext(notificacao, "API Externa / Service", "Serviço externo (ex: e-mail/notificação) para envio de avisos de reservas e aprovações.")

    Rel(solicitante, sistema, "Consulta horários, cria e altera solicitações de reserva", "HTTP / REST API")
    Rel(responsavel, sistema, "Aprova solicitações restritas e registra retiradas/devoluções", "HTTP / REST API")
    Rel(admin, sistema, "Gerencia cadastros, bloqueios e manutenções de recursos", "HTTP / REST API")

    Rel(sistema, db, "Realiza CRUD, controle concorrente de reservas e gravação de auditoria", "JDBC / Port 5432")
    Rel(sistema, notificacao, "Envia dados de notificação simulada ou real", "HTTP / REST Client")

    C4Component
    title Diagrama de Componentes - Backend Spring Boot

    Container_Boundary(backend, "Aplicação Backend (Java 21 / Spring Boot 3.x)") {
        Component(auth, "Security & Auth", "Spring Security", "Trata autenticação JWT/Session e autorização por perfil (SOLICITANTE, RESPONSAVEL, ADMIN).")
        Component(controller, "REST Controllers", "Spring MVC", "Expõe os endpoints da API (Salas, Professores, Materiais, Reservas).")
        Component(service, "Services & Concurrency Control", "Spring Service", "Executa regras de negócio, validação de sobreposição e trava pessimista/otimista contra dupla reserva.")
        Component(audit, "Audit Aspect/Service", "Spring AOP", "Captura mudanças de estado (SOLICITADA -> APROVADA -> EM_USO) e gera histórico auditável.")
        Component(repository, "Repositories", "Spring Data JPA", "Mapeamento e persistência das entidades no banco de dados.")
        Component(flyway, "Flyway Migrations", "Flyway Engine", "Gerencia a evolução do schema do banco de dados (V1, V2...).")
    }

    ContainerDb(postgres, "PostgreSQL 16", "Database", "Armazena tabelas de recursos, reservas, usuários e auditoria.")

    Rel(controller, auth, "Valida perfil e permissão do usuário")
    Rel(controller, service, "Delega regras de negócio")
    Rel(service, audit, "Notifica transições de estados")
    Rel(service, repository, "Consulta e salva dados de reservas e recursos")
    Rel(audit, repository, "Salva logs de auditoria no banco")
    Rel(repository, postgres, "Executa SQL / Transactions", "Port 5432")
    Rel(flyway, postgres, "Aplica DDLs e versionamento", "JDBC")

    erDiagram
    TB_USUARIOS {
        BIGINT id PK
        VARCHAR nome
        VARCHAR email UK
        VARCHAR senha
        VARCHAR perfil "SOLICITANTE, RESPONSAVEL, ADMIN"
    }

    TB_RECURSOS {
        BIGINT id PK
        VARCHAR nome
        VARCHAR tipo "SALA, MATERIAL, EQUIPAMENTO"
        INT capacidade "Requisito de capacidade da sala"
        VARCHAR localizacao "Bloco / Prédio / Sala"
        BOOLEAN eh_restrito "Exige aprovação obrigatória"
        BOOLEAN em_manutencao "Bloqueio automático para reservas"
    }

    TB_PROFESSORES {
        BIGINT id PK
        BIGINT usuario_id FK
        VARCHAR competencias "Competências técnicas/acadêmicas"
    }

    TB_RESERVAS {
        BIGINT id PK
        BIGINT recurso_id FK
        BIGINT solicitante_id FK
        BIGINT professor_id FK "Opcional: Alocação docente"
        TIMESTAMP data_hora_inicio
        TIMESTAMP data_hora_fim
        VARCHAR status "SOLICITADA, APROVADA, REJEITADA, EM_USO, CONCLUIDA, CANCELADA, NAO_COMPARECEU"
        TIMESTAMP created_at
    }

    TB_AUDITORIA {
        BIGINT id PK
        BIGINT reserva_id FK
        BIGINT usuario_id FK
        VARCHAR estado_anterior
        VARCHAR estado_novo
        TIMESTAMP data_transicao
        VARCHAR motivo
    }

    TB_USUARIOS ||--o{ TB_RESERVAS : "solicita"
    TB_RECURSOS ||--o{ TB_RESERVAS : "é alocado em"
    TB_PROFESSORES ||--o{ TB_RESERVAS : "alocado como docente"
    TB_USUARIOS ||--o| TB_PROFESSORES : "possui perfil docente"
    TB_RESERVAS ||--o{ TB_AUDITORIA : "gera histórico"