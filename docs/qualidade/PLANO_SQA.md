# Plano de SQA e Gates de Prontidão

> Status: planejamento da Issue #109. SQA avalia processo e produto; não substitui testes.

## Papéis e independência

- Autor implementa e apresenta evidências.
- Revisor diferente verifica critérios, testes, riscos e documentação.
- Quem desenvolveu pode mover para revisão, mas não autoaprova item crítico.
- Os quatro integrantes conhecem as decisões principais; autoria não cria ilhas de conhecimento.

## Verificação e validação

- **Verificação:** código, teste, configuração e documento atendem especificações e padrões.
- **Validação:** o fluxo resolve a necessidade das personas nas condições declaradas.
- Testes, análise estática, revisão e demonstração são evidências complementares.

## Gates

### Entrada em Ready

- requisito/risco e critério observável;
- dependências concluídas;
- casos e técnica definidos;
- dados, ambiente e evidência planejados;
- impacto de segurança avaliado;
- responsável somente após a divisão oficial.

### Entrada em Review

- commits pequenos, separados por alteração e referenciando a Issue;
- código, teste, configuração, documento e evidência em commits separados;
- `./mvnw -B verify` executado;
- PR aberto sem squash;
- RTM e riscos atualizados.

### Entrada em Done

- revisão por par aprovada;
- pipeline e gates aplicáveis verdes;
- assertions relevantes e teste de regressão quando houver defeito;
- documentação e evidência correspondem ao commit;
- nenhuma pendência crítica escondida.

### Gate da release

A decisão é **REPROVADA** se houver:

- aplicação que não inicia em clone limpo;
- pipeline ausente ou vermelho;
- falha de autorização;
- possibilidade de dupla reserva;
- bug ou vulnerabilidade crítica confirmada e aberta;
- RTM crítica órfã ou evidência de outro commit.

## Itens não testados e risco residual

Toda exclusão declara justificativa, responsável pela decisão, impacto, mitigação e prazo. Resultado pendente nunca é preenchido como aprovado.

## Checklist independente

- [ ] Clonar em diretório limpo e seguir somente o README.
- [ ] Conferir tag/commit, ambiente e massa.
- [ ] Abrir links de RTM e artifacts.
- [ ] Verificar resultados funcionais, integração, segurança, cobertura e carga.
- [ ] Procurar segredo/dado pessoal em código, log, JTL, HTML e artifacts.
- [ ] Conferir autoria dos quatro integrantes e revisões cruzadas.
- [ ] Registrar achados, severidade, prioridade e próxima ação.
- [ ] Emitir conclusão proporcional à evidência e declarar limitações.
