# Plano de Testes

> Status: planejamento. Resultados reais pertencem a RELATORIO_DE_TESTES.md e devem identificar commit, ambiente e execução.

## Objetivo

Validar requisitos, regras críticas, segurança, concorrência, integrações, interface e desempenho com a técnica mais baixa que capture cada risco.

## Escopo e níveis

| Nível/técnica | Ferramenta/ambiente | Protege | Issues |
|---|---|---|---|
| unidade | JUnit 5, JVM isolada | regras, limites e estados | #14, #27–#31, #102 |
| parametrizado | JUnit Jupiter | equivalências, decisão e fronteiras | #22, #27, #31 |
| integração | Spring Boot Test + PostgreSQL/Testcontainers | JPA, migration, constraint e transação | #18, #33, #38, #44, #49 |
| API caixa-preta/cinza | HTTP sobre aplicação completa | contrato, autorização, erro e ausência de campos | #50, #102, #110 |
| E2E | interface + API + banco | jornadas das três personas | #50, #62–#75 |
| integração externa | WireMock | request, mapping e degradação | #42, #104 |
| concorrência | duas transações sincronizadas | uma única reserva aceita | #33 |
| TDD/BDD | Red–Green–Refactor + Given–When–Then | funcionalidade nova | #51, #105 |
| carga | JMeter não gráfico | p95/p99, erro, throughput e invariantes | #54, #106 |
| segurança | testes de perfil/propriedade + scanners | autorização, segredos e vulnerabilidades | #14, #92–#98, #110 |

## Técnicas de projeto

- Partição de equivalência.
- Análise de valor limite: antes/no/depois.
- Tabelas de decisão para perfil, propriedade, restrição, manutenção e estados.
- Caixa-branca para ramo/condição.
- Caixa-cinza para API com conhecimento dos riscos internos.
- Testes positivos, negativos e de ausência.
- Catálogo detalhado em [CASOS_DE_TESTE.md](CASOS_DE_TESTE.md).
- Regras em [TECNICAS_DE_PROJETO.md](TECNICAS_DE_PROJETO.md).

## Anatomia obrigatória do caso

ID, título, origem, nível/técnica, pré-condições, dados sintéticos concretos, passos numerados, resultado mensurável, automação, evidência e status. Outra pessoa deve conseguir executar sem explicação oral.

## Propriedades da suíte

- rápida, independente, repetível e autoavaliável;
- fixture pequena e uma regra principal por teste;
- nomes descrevem contexto, ação e resultado;
- nenhuma rede, banco, data real, ordem global ou `Thread.sleep()` em unidade;
- `Clock` ou instante explícito para regras temporais;
- assertions verificam decisão e efeitos colaterais essenciais;
- teste negativo prova banco inalterado e campo sensível ausente;
- teste de sanidade fica vermelho quando a regra protegida é removida/alterada em branch de trabalho.

## Uso de dublês

- Dummy: preenche parâmetro irrelevante.
- Stub: fornece resposta programada.
- Mock: verifica interação obrigatória.
- Fake: implementação simplificada em memória.
- Spy: uso excepcional e justificado.
- Mockito somente em unidade isolada.
- Persistência/constraint/transação crítica com PostgreSQL/Testcontainers.
- Contrato HTTP e falhas externas com WireMock.
- Poucos testes opcionais contra sandbox real, sem substituir a suíte determinística.

## Critérios de entrada

- requisito e aceite numerados no PRD;
- risco e técnica definidos;
- ambiente e massa controlados;
- dependências concluídas;
- caso na RTM;
- versão/commit identificados.

## Critérios de suspensão

Suspender o ciclo e devolver para correção quando:

- ambiente não é reproduzível;
- massa contém dado real/segredo;
- resultado é intermitente sem diagnóstico;
- migration ou aplicação não inicia;
- evidência não corresponde ao commit;
- limitador de nota impede conclusão útil.

## Critérios de saída

- suíte aplicável executada com `./mvnw -B verify`;
- 80% linhas e 70% branches globais, com atenção maior às regras críticas;
- 100% das regras críticas na RTM;
- zero bug/vulnerabilidade crítica confirmada e aberta;
- nenhum teste ignorado/instável sem decisão registrada;
- evidências da mesma release.

## CI e artifacts

O contrato principal é `./mvnw -B verify`. A CI roda em PR e push da main, com Java 21, cache Maven, timeout, concorrência, permissões mínimas e artifacts Surefire/Failsafe/JaCoCo em sucesso e falha. #103 comprova execução vermelha, correção verde e merge bloqueado.

## JaCoCo e SonarCloud

- JaCoCo 0.8.15: prepare-agent, report HTML/XML e check na fase verify.
- Não usar Surefire com `forkCount=0`.
- Não excluir classe relevante para bater meta.
- Sonar usa histórico completo, relatório XML e token em secret.
- Quality Gate revisa novo código; issue é corrigida, aceita com justificativa ou marcada falso positivo com evidência.

## WireMock e integrações

Cobrir 2xx válido, 4xx, 5xx, timeout e JSON ausente/malformado. URL, credencial e timeout são configuráveis. Retry só é permitido com operação idempotente, limite e backoff. Corpo externo e segredo não atravessam resposta/log.

## Carga

#106 define carga, usuários/taxa, distribuição, think time, rampa, aquecimento, patamar, duração, massa, p95, p99, throughput e erro. O resultado inclui JMX, JTL, HTML, ambiente, commit e verificação posterior de uma única reserva aceita.

## Segurança

Gate inclui autorização horizontal/vertical, entrada, erro seguro, CodeQL, SCA/dependency review, segredo/push protection, Trivy, ZAP e testes de ausência. Scanner crítico não triado bloqueia a release.

## Rastreabilidade

A RTM liga PRD → risco/ATAM → caso → classe/JMX → execução → conclusão. #107 procura requisito órfão, teste fantasma, evidência vencida e link quebrado.

## Itens fora do teste atual

Enquanto a equipe não aprovar outra decisão: pagamento real, aplicativo mobile nativo, push real, multi-região e garantia de produção. A exclusão é risco declarado, não silêncio.
