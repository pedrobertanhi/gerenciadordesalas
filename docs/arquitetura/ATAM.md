# ATAM enxuto e Utility Tree

> Status: modelo inicial a ser validado pelos quatro integrantes na Issue #100. Números de performance são hipóteses até a aprovação da #106.

## 1. Direcionadores

- Impedir conflitos e corrupção de agenda.
- Proteger ações por identidade, perfil e propriedade.
- Manter histórico auditável.
- Produzir resposta previsível sob concorrência e falhas externas.
- Permitir evolução sem espalhar regras por controllers.

## 2. Utility tree

| ID | Atributo ISO/IEC 25010 | Refinamento | Cenário resumido | I/D |
|---|---|---|---|---|
| QA-SEC-01 | Segurança | Autorização | Solicitante tenta alterar reserva de outra pessoa e recebe negação sem mudança nem vazamento | A/A |
| QA-CON-01 | Confiabilidade | Concorrência | Duas requisições disputam a mesma sala e apenas uma é aceita | A/A |
| QA-PERF-01 | Eficiência | Latência sob carga | Reservas na carga nominal respeitam p95/p99 e taxa de erro definidos | A/A |
| QA-AUD-01 | Confiabilidade | Rastreabilidade | Toda transição gera evento auditável sem perder a operação | A/M |
| QA-INT-01 | Compatibilidade | Degradação externa | Notificação lenta ou inválida não corrompe reserva nem vaza corpo externo | M/M |
| QA-MAN-01 | Manutenibilidade | Modularidade | Trocar o provedor de notificação afeta apenas a porta, o adapter e sua configuração | M/M |
| QA-USE-01 | Capacidade de interação | Erro compreensível | Entrada inválida mostra ação corretiva em desktop e mobile | M/B |
| QA-FLEX-01 | Flexibilidade | Instalação | Clone limpo inicia com comandos e configuração de exemplo do README | A/M |

## 3. Cenários prioritários nas seis partes

### QA-SEC-01 — autorização horizontal

1. **Fonte:** usuário autenticado no perfil Solicitante.
2. **Estímulo:** envia alteração para reserva pertencente a outro solicitante.
3. **Ambiente:** aplicação normal com PostgreSQL e reserva existente.
4. **Artefato:** endpoint/caso de uso de alteração de reserva.
5. **Resposta:** negar por padrão, não alterar o banco e registrar correlação.
6. **Medida:** 100% dos casos da matriz negados; corpo sem dados da reserva, token, SQL ou stack trace.

### QA-CON-01 — dupla reserva

1. **Fonte:** dois clientes sincronizados.
2. **Estímulo:** criam a mesma reserva de sala para o mesmo intervalo.
3. **Ambiente:** aplicação e PostgreSQL reais em container.
4. **Artefato:** transação e restrição de disponibilidade.
5. **Resposta:** aceitar uma solicitação e rejeitar a outra com conflito controlado.
6. **Medida:** exatamente uma reserva válida persistida em cada execução do teste automatizado.

### QA-PERF-01 — carga nominal

1. **Fonte:** usuários virtuais JMeter.
2. **Estímulo:** pesquisam disponibilidade e criam reservas segundo distribuição documentada.
3. **Ambiente:** commit, Java, banco, máquina, massa, rampa e duração registrados.
4. **Artefato:** API de disponibilidade/reserva e banco.
5. **Resposta:** manter serviço e invariantes.
6. **Medida:** p95, p99, throughput e erros dentro dos limites aprovados em #106; disputa mantém uma aceitação.

## 4. Achados arquiteturais

### Riscos

- Verificar disponibilidade e gravar em transações separadas permite condição de corrida.
- Repetir autorização em controllers cria decisões divergentes.
- Logar payloads e headers pode expor credenciais e dados.
- Usar banco em memória nos testes pode ocultar constraints do PostgreSQL.

### Não-riscos condicionais

- WireMock é adequado para testar nossa reação ao contrato externo, **desde que** fique explícito que não prova o provedor real.
- Arquitetura em camadas/portas é suficiente para a integração prevista, **desde que** a regra de negócio permaneça independente de HTTP e JPA.

### Ponto de sensibilidade

A granularidade do bloqueio/constraint transacional move diretamente throughput, espera e taxa de conflitos.

### Ponto de tradeoff

Maior serialização da reserva aumenta integridade sob concorrência, mas pode reduzir throughput e elevar latência. A decisão deve ser tomada com o teste concorrente e o JMeter, não por opinião.

## 5. Tradução para testes e decisões

| Cenário | Casos/Issues | Evidência futura |
|---|---|---|
| QA-SEC-01 | #14, #110 | teste de API + banco inalterado + logs sanitizados |
| QA-CON-01 | #32, #33, #54 | Testcontainers + execução concorrente |
| QA-PERF-01 | #54, #106 | JMX, JTL, HTML, métricas e conclusão |
| QA-AUD-01 | #39, #40, #88 | consulta de auditoria e teste de transição |
| QA-INT-01 | #42, #104 | WireMock 2xx/4xx/5xx/timeout/inválido |
| QA-FLEX-01 | #80, #109 | instalação em diretório limpo |

## 6. Regra de uso

O ATAM identifica riscos e tradeoffs; não aprova a arquitetura. Alterações de premissa devem reabrir a análise, atualizar ADR, risco, caso e RTM.
