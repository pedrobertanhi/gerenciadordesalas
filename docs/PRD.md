# PRD — Gerenciador de Salas de Aula

> Status: planejamento. Este documento define o produto; conclusão funcional exige código, testes e evidências reais.

## 1. Visão do produto

Aplicação para organizar salas, professores, materiais e equipamentos acadêmicos sem conflitos de horário, com segurança, auditoria e evidências objetivas de qualidade.

## 2. Problema

Reservas descentralizadas podem produzir sobreposição de sala, choque na agenda docente, indisponibilidade de material, uso durante manutenção e decisões sem histórico. O sistema deve centralizar essas decisões e impedir estados inválidos, inclusive sob solicitações simultâneas.

## 3. Personas

### Solicitante — professor ou coordenador

- **Objetivo:** encontrar recursos disponíveis e criar, alterar ou cancelar as próprias reservas.
- **Contexto:** trabalha com horários, turmas, capacidade, localização e competências docentes.
- **Frustrações:** descobrir conflito tarde, receber erro incompreensível ou não saber o estado da solicitação.

### Responsável

- **Objetivo:** aprovar recursos restritos, validar docentes e acompanhar retirada/devolução.
- **Contexto:** toma decisões especiais e precisa de fila, justificativa e histórico.
- **Frustrações:** aprovar sem contexto, perder rastreabilidade ou não identificar material em atraso.

### Administrador

- **Objetivo:** manter usuários, recursos, bloqueios e períodos de manutenção.
- **Contexto:** protege integridade cadastral e operacional.
- **Frustrações:** dados duplicados, exclusão de histórico e permissões ambíguas.

## 4. Escopo funcional obrigatório

| ID | Comportamento | Critério observável resumido |
|---|---|---|
| RF-01 | Autenticar e autorizar por perfil | Ação negada por padrão quando identidade, perfil ou propriedade não autorizam |
| RF-02 | Cadastrar e consultar salas, professores e materiais | Dados válidos persistem; duplicidade e entrada inválida são recusadas |
| RF-03 | Pesquisar recursos | Filtros por tipo, capacidade, localização, competência e disponibilidade podem ser combinados |
| RF-04 | Criar, alterar e cancelar reservas | Somente solicitante autorizado atua sobre a própria reserva; transições respeitam estado |
| RF-05 | Detectar sobreposição | Sala, professor e material não se sobrepõem no intervalo reservado |
| RF-06 | Impedir dupla reserva concorrente | Duas solicitações simultâneas para o mesmo recurso produzem uma única aceitação |
| RF-07 | Aprovar recurso restrito | Reserva restrita permanece solicitada até decisão de Responsável |
| RF-08 | Bloquear manutenção | Recurso bloqueado no intervalo não pode ser reservado |
| RF-09 | Registrar retirada e devolução | Quantidade, instante, responsável e situação ficam rastreáveis |
| RF-10 | Manter histórico auditável | Toda mudança relevante registra autor, instante, origem e transição |
| RF-11 | Notificar | Evento gera notificação simulada ou chamada externa com degradação controlada |
| RF-12 | Emitir relatórios | Utilização por recurso, carga docente e conflitos evitados são calculados com período explícito |
| RF-13 | Oferecer interface responsiva e erros claros | Fluxos funcionam em desktop/mobile e erro público não expõe detalhe interno |
| RF-14 | Documentar API/fluxos | Contratos, autenticação, erros e roteiro público são navegáveis e atualizados |

## 5. Regras críticas

- RC-01: término deve ser posterior ao início.
- RC-02: intervalos adjacentes são permitidos; sobreposição real é proibida.
- RC-03: sala não pode possuir reservas conflitantes.
- RC-04: professor não pode possuir reservas conflitantes.
- RC-05: material não pode exceder a quantidade disponível no intervalo.
- RC-06: concorrência aceita no máximo uma reserva para a mesma disponibilidade.
- RC-07: manutenção impede reserva no intervalo afetado.
- RC-08: fluxo principal é `SOLICITADA → APROVADA → EM_USO → CONCLUIDA`.
- RC-09: estados alternativos são `REJEITADA`, `CANCELADA` e `NAO_COMPARECEU`.
- RC-10: somente Responsável aprova recurso restrito.
- RC-11: reserva iniciada não pode ser apagada.
- RC-12: toda mudança de estado gera auditoria.

## 6. Requisitos não funcionais mensuráveis

| ID | Requisito e critério |
|---|---|
| RNF-QUAL-01 | Cobertura global mínima de 80% de linhas e 70% de branches, sem testes triviais para inflar indicador |
| RNF-TRACE-01 | 100% das regras críticas na RTM com requisito, risco, caso, automação, execução e status |
| RNF-SEC-01 | Zero bug ou vulnerabilidade crítica confirmada e aberta na release |
| RNF-CI-01 | `./mvnw -B verify` executa em PR e push da main; falha bloqueia merge |
| RNF-CONC-01 | Sob disputa controlada do mesmo recurso, apenas uma reserva é aceita e a invariante é verificada no banco |
| RNF-PERF-01 | Limites de p95, p99, taxa de erro, throughput, carga e duração serão fechados em #106 antes do JMeter |
| RNF-ERR-01 | Respostas de erro usam código estável e correlação, sem stack trace, SQL, token ou corpo externo |
| RNF-REP-01 | Clone limpo executa seguindo apenas o README no commit/tag entregue |

## 7. Estados e políticas

A máquina de estados deve negar qualquer transição não explicitamente permitida. A política de autorização fica centralizada fora dos controllers, usa negação por padrão e distingue autenticação, perfil, propriedade, recurso e estado.

A escolha entre 403 e 404 para ocultar recursos restritos deve ser única, documentada e testada para evitar enumeração.

## 8. Fora de escopo inicial

- Pagamento real.
- Aplicativo móvel nativo.
- Notificação push real.
- Integração com dados pessoais reais.
- Alta disponibilidade multi-região.
- Garantia de produção além dos ambientes e cargas realmente ensaiados.

Qualquer mudança de escopo exige Issue, revisão do PRD, impacto na RTM, casos e planejamento.

## 9. Métricas de sucesso

- Todos os RF e RC possuem caso e evidência navegável na RTM.
- Pipeline, JaCoCo, SonarCloud e gates de segurança passam no commit entregue.
- Teste automatizado comprova a proteção contra dupla reserva.
- Nenhum limitador de nota permanece: aplicação executa, pipeline existe, autorização funciona e dupla reserva é impedida.
- Cada integrante possui commits relevantes de código e testes e revisa trabalho de outro integrante.

## 10. Evidência e status

Issue planejada não significa requisito concluído. O status só muda com implementação, teste apropriado, revisão por pares e execução vinculada ao mesmo commit ou tag da entrega.
