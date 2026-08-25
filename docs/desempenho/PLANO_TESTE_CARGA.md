# Plano de Teste de Carga com JMeter

> Status: hipótese da #54/#106. Os limites finais devem ser aprovados antes da execução.

## RNF

Durante a janela medida, sob carga e distribuição documentadas, pesquisa e reserva devem respeitar p95, p99, throughput e taxa de erro aprovados, mantendo a invariante de exatamente uma reserva aceita quando duas solicitações disputam a mesma disponibilidade.

| Parâmetro | Valor planejado |
|---|---|
| operação | pesquisa de disponibilidade + criação de reserva |
| ambiente | registrar commit, Java, Spring, PostgreSQL, CPU, memória e rede |
| aquecimento | 2 minutos |
| patamar | 10 minutos |
| usuários/taxa | definir em #106 após linha de base |
| rampa | definir em #106 |
| think time | modelado e versionado |
| p95/p99 | limites numéricos aprovados em #106 |
| erro | limite numérico aprovado em #106 |
| integridade | exatamente uma reserva aceita por disputa |

## Tipo de experimento

O obrigatório é **load test** nominal. Stress, spike, endurance e volume só podem receber esses nomes quando executados com desenho próprio; não extrapolar conclusão.

## Modelo de carga

- perfis e operações com distribuição percentual;
- usuários/taxa de chegada, rampa, patamar e duração;
- massa sintética única por usuário nas escritas;
- cenário isolado disputando o mesmo recurso;
- estado inicial do banco e índices identificados;
- linha de base antes de otimizar.

## Estrutura JMeter

Thread Group; HTTP Request Defaults; CSV Data Set; login/autenticação se necessária; JSON Extractor; Header Manager; Requests; Response Assertions; Timers. Listener gráfico somente para depuração leve; execução final em modo não gráfico.

## Comando reproduzível

```bash
jmeter -n   -t performance/gerenciador-reservas.jmx   -JbaseUrl=http://localhost:8080   -Jthreads=<definir> -Jramp=<definir> -Jduration=600   -l performance/resultados.jtl   -e -o performance/relatorio
```

Não versionar senha/token real em JMX, CSV, JTL ou HTML.

## Evidências

- JMX e massa sintética versionados;
- comando, commit e ambiente;
- JTL e HTML sanitizados;
- CPU, memória e pool de conexões;
- consulta final provando a invariante;
- linha de base e pelo menos três execuções comparáveis, ou justificativa;
- conclusão com dado, hipótese, intervenção proposta e novo experimento.

## Critério de conclusão

Começar por erro, p95/p99 e invariante; depois usar recursos para hipóteses. “O banco é lento” não é diagnóstico. A conclusão declara afirmação, evidência, interpretação e limite do experimento.
