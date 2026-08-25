# Catálogo de Casos de Teste

> Status: planejamento. IDs são estáveis; automação e execução serão vinculadas quando existirem.

## Template obrigatório

| Campo | Conteúdo |
|---|---|
| ID | Identificador único e estável |
| Título | Comportamento verificado em uma linha |
| Origem | RF, RNF, regra crítica, risco ou cenário ATAM |
| Nível/técnica | Unidade, integração, API, E2E, carga; equivalência, limite ou decisão |
| Pré-condições | Estado concreto antes da ação |
| Dados | Valores sintéticos e reproduzíveis |
| Passos | Sequência numerada e independente |
| Resultado esperado | Oráculo objetivo, incluindo efeitos ausentes |
| Automação | Classe/método ou JMX, quando criado |
| Evidência | Commit, PR, execução e artifact do mesmo escopo |
| Status | Planejado, automatizado, aprovado, falhou, bloqueado ou não executado |

## Casos críticos iniciais

### CT-TEMPO-01 — término igual ao início

- **Origem:** RC-01 / RF-04.
- **Nível/técnica:** unidade e API; valor limite.
- **Pré-condições:** solicitante autorizado e recursos ativos.
- **Dados:** início `2026-09-21T10:00:00-03:00`; término igual.
- **Passos:** enviar criação de reserva.
- **Esperado:** validação recusada, nenhuma reserva persistida e código de erro estável.

### CT-TEMPO-02 — intervalos adjacentes

- **Origem:** RC-02 / RF-05.
- **Nível/técnica:** integração; valor limite.
- **Pré-condições:** reserva existente das 10:00 às 11:00.
- **Dados:** nova reserva das 11:00 às 12:00 para a mesma sala.
- **Passos:** criar a segunda reserva.
- **Esperado:** aceita, pois não existe interseção real.

### CT-CONC-01 — disputa simultânea da mesma sala

- **Origem:** RC-03, RC-06 / QA-CON-01.
- **Nível/técnica:** integração concorrente com PostgreSQL/Testcontainers.
- **Pré-condições:** sala ativa e livre; duas transações sincronizadas.
- **Dados:** mesmo recurso e intervalo.
- **Passos:** liberar as duas solicitações ao mesmo tempo; aguardar; consultar banco.
- **Esperado:** uma aceitação, um conflito controlado e exatamente uma reserva válida.

### CT-PROF-01 — choque na agenda docente

- **Origem:** RC-04 / RF-05.
- **Nível/técnica:** integração e tabela de decisão.
- **Pré-condições:** professor já alocado no intervalo em outra sala.
- **Dados:** segunda sala, mesmo professor e período sobreposto.
- **Passos:** solicitar nova reserva.
- **Esperado:** rejeição por conflito do professor e nenhuma gravação.

### CT-MAT-01 — quantidade insuficiente

- **Origem:** RC-05 / RF-05.
- **Nível/técnica:** integração; valores limite.
- **Pré-condições:** dez unidades totais e oito já reservadas.
- **Dados:** solicitar 2 e 3 unidades em casos independentes.
- **Esperado:** 2 é aceito no limite; 3 é recusado; quantidade nunca fica negativa.

### CT-MNT-01 — recurso em manutenção

- **Origem:** RC-07 / RF-08.
- **Nível/técnica:** unidade, integração e API.
- **Pré-condições:** bloqueio ativo que intersecta o intervalo.
- **Dados:** sala e período bloqueados.
- **Passos:** solicitar reserva.
- **Esperado:** conflito de manutenção, zero reserva e histórico preservado.

### CT-AUTH-01 — solicitante altera reserva alheia

- **Origem:** RF-01 / QA-SEC-01.
- **Nível/técnica:** API caixa-preta e autorização horizontal.
- **Pré-condições:** usuário A autenticado; reserva pertencente a B.
- **Dados:** alteração válida enviada por A.
- **Passos:** chamar endpoint; consultar banco e logs sanitizados.
- **Esperado:** negação consistente; estado inalterado; resposta/log sem dados da reserva, token, SQL ou stack trace.

### CT-APR-01 — aprovação restrita por perfil incorreto

- **Origem:** RC-10 / RF-07.
- **Nível/técnica:** unidade, API e tabela de decisão.
- **Pré-condições:** reserva restrita em SOLICITADA.
- **Dados:** decisões por Solicitante, Administrador e Responsável.
- **Passos:** tentar aprovar em execuções independentes.
- **Esperado:** somente Responsável aprova; negativas mantêm estado e geram auditoria quando aplicável.

### CT-STATE-01 — transição inválida

- **Origem:** RC-08, RC-09.
- **Nível/técnica:** unidade parametrizada.
- **Pré-condições:** reserva em cada estado de origem.
- **Dados:** tabela completa origem × evento.
- **Passos:** solicitar transição.
- **Esperado:** transições permitidas resultam no estado correto; demais são negadas com código estável.

### CT-DEL-01 — exclusão após início

- **Origem:** RC-11.
- **Nível/técnica:** integração/API.
- **Pré-condições:** reserva EM_USO.
- **Passos:** solicitar exclusão.
- **Esperado:** operação negada; reserva e auditoria preservadas.

### CT-AUD-01 — auditoria de estado

- **Origem:** RC-12 / RF-10.
- **Nível/técnica:** integração.
- **Pré-condições:** usuário e reserva identificados.
- **Passos:** executar transição válida; consultar histórico.
- **Esperado:** um registro com reserva, origem, destino, autor, instante controlado e correlação.

### CT-INT-01 — provedor retorna erro sensível

- **Origem:** RF-11 / QA-INT-01.
- **Nível/técnica:** WireMock/API.
- **Pré-condições:** provedor simulado devolve 500 e inclui token fictício no corpo.
- **Passos:** disparar notificação; observar resposta e logs.
- **Esperado:** erro interno neutro; token/corpo não atravessa a API nem os logs; reserva não é corrompida.

## Execução cruzada

Antes de fechar #101, outro integrante deve executar uma amostra sem explicação oral. Dúvida ou dependência implícita significa caso incompleto. Nenhum caso usa senha, token, matrícula ou dado pessoal real.
