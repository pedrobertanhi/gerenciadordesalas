# Roteiro da Apresentação e Prova Oral

> Status: planejamento da #79 e #108. Duração-alvo: 8 minutos.

## Roteiro cronometrado

| Tempo | Conteúdo | Evidência aberta |
|---|---|---|
| 0:00–0:45 | problema, personas, escopo e risco crítico | PRD + ATAM |
| 0:45–2:00 | arquitetura, concorrência e tradeoff | diagramas + ADR/ATAM |
| 2:00–4:30 | demo: um permitido e três negados | aplicação e banco |
| 4:30–6:30 | JUnit, CI, JaCoCo/Sonar, WireMock/Testcontainers, carga e segurança | pipeline/artifacts |
| 6:30–7:30 | requisito → risco → caso → automação → execução | RTM |
| 7:30–8:00 | limitações, falhas conhecidas e decisão | relatório/parecer |

## Demo mínima

1. Solicitante cria reserva válida.
2. Segundo solicitante disputa o mesmo recurso e recebe conflito.
3. Perfil não autorizado tenta aprovar recurso restrito e é negado.
4. Reserva de recurso em manutenção é negada.
5. Mostrar auditoria e ausência de detalhe sensível.

Usar dados sintéticos preparados. A demo mostra regras e riscos, não todas as telas.

## Banco de perguntas

Cada integrante precisa responder:

- Qual atributo de qualidade orientou a arquitetura e qual tradeoff surgiu?
- Por que cobertura alta não prova correção?
- O que caixa-preta encontra que caixa-branca pode não encontrar, e vice-versa?
- Qual a diferença entre WireMock e Testcontainers?
- Como o ciclo TDD/BDD mudou o design escolhido?
- Qual carga o JMeter simulou e qual conclusão é permitida?
- Como o sistema impede dupla reserva?
- Como autenticação difere de autorização por perfil/propriedade?
- Mostre na RTM um requisito, risco, caso e a última execução.
- Qual falha/risco residual tem maior impacto?
- O que não foi testado e por quê?
- Que teste adicional seria o próximo?

Divisão de tarefas não permite ilhas de conhecimento.

## Dois ensaios em pares

1. Grupo A apresenta em até 8 minutos.
2. Grupo B sorteia três perguntas e exige abrir a evidência.
3. B avalia domínio, evidência, RTM e comunicação de 0 a 2.
4. A abre Issues para as três correções prioritárias.
5. Trocar papéis.

| Dimensão | 0 | 1 | 2 |
|---|---|---|---|
| domínio | não explica | parcial | regra + tradeoff |
| evidência | opinião | artefato vago | execução identificada |
| RTM | não navega | link parcial | cadeia completa |
| comunicação | contraditória | depende de um membro | clara e compartilhada |

## Contingência

- Gravar previamente links, logs sanitizados e capturas com commit/data.
- Manter comandos de diagnóstico no README.
- Se a demo falhar, declarar sintoma e último estado verde.
- Identificar evidência anterior como anterior.
- Explicar hipótese, próxima verificação e impacto na conclusão.
- Não alterar código apressadamente ao vivo nem apresentar captura antiga como atual.

## Gate de prontidão

- [ ] Clone limpo executa pelo README.
- [ ] Pipeline do commit/tag está verde ou falhas estão declaradas.
- [ ] RTM sem órfãos, fantasmas, evidência vencida ou links quebrados.
- [ ] Relatório contém escopo, ambiente, resultados, interpretação e limites.
- [ ] Repositório/evidências não contêm segredo ou dado real.
- [ ] Demo possui dados, tempo e contingência.
- [ ] Os quatro integrantes dominam as decisões.
- [ ] Tag, commit e link final foram conferidos.
