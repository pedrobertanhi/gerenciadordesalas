# Cobertura da Especificação e dos Materiais

> Auditoria de planejamento em 25/08/2026. Issue planejada não é evidência de conclusão.

## Fontes revisadas

- Especificação oficial “Organização de Recursos”.
- Introdução à Qualidade de Software.
- ATAM e Planejamento de Casos de Teste.
- Caixa Branca vs Caixa Preta.
- Introdução a Testes Automatizados.
- Gestão de Erros e Bugs.
- Testes Automatizados em CI.
- JUnit 5, JaCoCo e SonarCloud.
- WireMock e APIs Seguras.
- TDD e BDD.
- JMeter.
- Projeto E4 (usado como exemplo de incremento vertical, adaptado ao domínio de reservas).
- Autorização e RTM (adaptada de plano/assinatura para perfil, propriedade, recurso e estado).
- Preparação para Prova Oral.
- O arquivo “Entrega Final.pfd.pdf” recebido possui uma página vazia e nenhum requisito extra legível.

## Requisitos funcionais

| ID | Exigência | Implementação/verificação |
|---|---|---|
| RF-01 | autenticação, perfil e usuários | #11–#14, #64, #85, #86, #110 |
| RF-02 | salas, professores e materiais | #15–#18, #62, #63, #66 |
| RF-03 | pesquisa e disponibilidade | #19, #20, #65 |
| RF-04 | ciclo de reservas | #23–#27, #59, #68, #70, #87 |
| RF-05 | sobreposição de sala, professor e material | #28–#31, #102 |
| RF-06 | dupla reserva concorrente | #32, #33, #54, #106 |
| RF-07 | aprovação restrita | #34, #35, #61, #69, #105 |
| RF-08 | manutenção | #21, #22, #67, #105 |
| RF-09 | retirada e devolução | #36–#38, #71 |
| RF-10 | auditoria | #39, #40, #72, #88 |
| RF-11 | notificação/integração | #41, #42, #104 |
| RF-12 | relatórios | #43, #44, #75 |
| RF-13 | interface e erros compreensíveis | #9, #45–#47, #62–#75, #86, #110 |
| RF-14 | documentação pública/API | #10, #80, README |

## Regras críticas

| Regra | Implementação | Casos/evidências |
|---|---|---|
| término posterior ao início | #27 | #31, #101, #102 |
| sala sem sobreposição | #28 | #31, #33, #101 |
| professor sem sobreposição | #29 | #31, #101 |
| quantidade de material preservada | #30 | #31, #101 |
| uma aceitação sob concorrência | #32 | #33, #54, #100, #101, #106 |
| manutenção impede reserva | #22 | #51, #101, #105 |
| estados principal e alternativos | #26, #87 | #38, #101, #102 |
| somente Responsável aprova restrito | #13, #35 | #14, #101, #105 |
| reserva iniciada não é apagada | #25, #59 | #101 |
| toda mudança de estado é auditada | #39, #88 | #40, #101 |

## Estratégia obrigatória de qualidade

| Exigência | Cobertura planejada |
|---|---|
| JUnit 5, parametrizados, integração, API e E2E | #14, #18, #27–#31, #38, #44, #48, #50, #101, #102 |
| PostgreSQL/Testcontainers | #18, #33, #38, #44, #49 |
| WireMock e degradação | #42, #104 |
| concorrência automatizada | #33 |
| TDD/BDD com evidência | #51, #105 |
| CI em PR, artifacts e merge gate | #52, #103 |
| JaCoCo 80/70 e SonarCloud | #53, #89 |
| JMeter e RNF mensurável | #54, #106 |
| autorização e erros seguros | #9, #13, #14, #110 |
| validação e mocks restritos | #9, #48, #49, #104 |
| segurança em camadas | #92–#98, #110 |

## Exigências adicionais encontradas nas aulas

| Exigência | Issue/documento |
|---|---|
| PRD, personas, fora de escopo e IDs estáveis | #99, PRD.md |
| ATAM: utility tree, seis partes, I/D, risco, não-risco, sensibilidade e tradeoff | #100, arquitetura/ATAM.md |
| casos com ID, pré-condição, dados, passos, oráculo e origem | #101, testes/CASOS_DE_TESTE.md |
| equivalência, limites, decisão e cobertura de ramo/condição | #102 |
| QA, QC, verificação, validação e SQA independente | #109 |
| relato reproduzível, severidade ≠ prioridade, causa raiz e regressão | #55, #74, #91 |
| CI vermelha→verde, artifacts em falha e proteção da main | #103 |
| Clock determinístico e ausência de Thread.sleep em unidade | #27, #48, #51, #102 |
| teste de sanidade que fica vermelho ao remover regra | #89, #102 |
| JaCoCo 0.8.15, relatório HTML/XML e check no verify | #53 |
| Sonar no PR, novo código e triagem justificável | #53 |
| WireMock 2xx/4xx/5xx/timeout/corpo inválido e não vazamento | #104, #110 |
| commits Red–Green–Refactor preservados | #105 |
| carga com rampa, patamar, think time, aquecimento e invariante | #106 |
| RTM órfã, teste fantasma e evidência vencida | #107 |
| relatório: afirmação, evidência, interpretação e limite | #77 |
| defesa oral de oito minutos, todos dominam o todo e contingência | #108 |

## Metas

- **80% linhas**: #53 e #89.
- **70% branches**: #31, #53 e #102.
- **100% regras críticas na RTM**: #55 e #107.
- **0 bugs/vulnerabilidades críticas**: #53, #74, #77, #83, #92–#98 e #110.

## Entregáveis

| Entregável | Controle |
|---|---|
| histórico, autoria e revisão | #4, #60 |
| aplicação e instalação | #5, #80 |
| README, PRD e API/fluxos | #10, #99 |
| RTM requisito–risco–teste–evidência | #55, #107 |
| diagramas de contexto, componentes e sequências | #3, #57 |
| plano, casos, suíte e relatório de testes | #48, #73, #101, #102 |
| CI, JaCoCo e SonarCloud | #52, #53, #103 |
| carga e resultado | #54, #106 |
| defeitos priorizados, causa raiz e regressão | #55, #74, #91 |
| segurança e parecer | #92–#98, #110 |
| relatório final, evidências e decisão | #77, #78, #83 |
| apresentação e prova oral | #79, #108 |
| release candidate | #82 |

## Auditoria final

A #107 só fecha depois de comprovar: zero requisito órfão, zero teste crítico fantasma, evidências do mesmo commit/tag e links válidos. Lacuna conhecida permanece explicitamente aberta; não se declara “100% concluído” com base apenas neste mapa.
