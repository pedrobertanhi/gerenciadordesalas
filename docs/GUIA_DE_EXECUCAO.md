# Guia de Execução de Cada Tarefa

> Este documento transforma uma Issue do backlog em trabalho concluído e comprovado. Ele não substitui os critérios específicos de cada Issue.

## 1. Antes de começar

- Confirmar que a Issue está em **Ready**.
- Verificar dependências, requisito, risco, critérios de aceite, testes e evidência esperada.
- Não assumir tarefa antes da divisão oficial entre os quatro integrantes.
- Conferir se outra pessoa já está trabalhando no mesmo arquivo ou regra.
- Atualizar a branch local a partir da `main`.

## 2. Criar a branch

Padrões:

```text
feature/numero-descricao
test/numero-descricao
fix/numero-descricao
docs/numero-descricao
ci/numero-descricao
```

Exemplo:

```bash
git switch main
git pull --ff-only
git switch -c feature/28-conflito-sala
```

Uma branch atende uma Issue principal. Trabalho adicional descoberto deve virar nova Issue quando possuir objetivo independente.

## 3. Planejar os microcommits antes de editar

Exemplo para uma funcionalidade:

1. entidade ou tipo de domínio;
2. migration;
3. repository;
4. regra/service;
5. DTO;
6. controller;
7. teste unitário;
8. teste de integração;
9. documentação;
10. RTM;
11. evidência.

A lista é adaptada à tarefa. Não criar commit vazio e não fragmentar uma alteração indivisível apenas para aumentar quantidade.

## 4. Regra literal de commits

- Cada alteração concluída em um arquivo gera commit próprio.
- Três arquivos alterados significam pelo menos três commits.
- Duas alterações independentes no mesmo arquivo significam dois commits.
- Código, teste, configuração, documentação e evidência ficam separados.
- Todo commit referencia a Issue.
- Correção pedida em review gera novo commit.
- Não usar `git commit --amend` para esconder a evolução.
- Pull Request não usa squash.

Exemplos:

```text
feat: adiciona entidade de reserva (#23)
db: cria migration de reservas (#23)
test: cobre término igual ao início (#27)
docs: liga CT-TEMPO-01 à RTM (#107)
fix: impede sobreposição da agenda docente (#29)
ci: publica relatórios mesmo após falha (#103)
```

## 5. Desenvolver na ordem correta

Para cada comportamento:

1. Confirmar regra no PRD.
2. Confirmar risco e caso de teste.
3. Implementar regra no domínio/caso de uso.
4. Proteger persistência e concorrência quando necessário.
5. Expor contrato seguro por DTO/API/UI.
6. Criar testes no nível adequado.
7. Executar localmente.
8. Atualizar RTM e documentação.
9. Guardar evidência do mesmo commit.

Evitar deixar todas as camadas parcialmente prontas. Priorizar incremento vertical demonstrável.

## 6. Testar localmente

Contrato principal:

```bash
./mvnw -B verify
```

Antes de a aplicação existir, esse comando continua pendente. Depois da Issue #5, ele deve executar testes unitários, integração, cobertura e verificações configuradas.

Conferir:

- cenário positivo;
- cenário negativo;
- valores-limite;
- efeitos que não podem ocorrer;
- autorização, quando aplicável;
- banco real com Testcontainers para persistência crítica;
- WireMock para integração HTTP;
- teste concorrente para disponibilidade;
- ausência de segredo e detalhe interno.

## 7. Teste de sanidade

Em branch de trabalho, alterar ou remover temporariamente a regra protegida. O teste correspondente precisa ficar vermelho pela razão esperada. Reverter essa alteração antes do commit final.

Isso prova que o teste protege a regra e não apenas executa linhas.

## 8. Atualizar rastreabilidade

A RTM deve ligar:

```text
requisito → risco → caso → automação → execução → conclusão
```

A evidência informa:

- Issue;
- classe/método ou JMX;
- comando;
- ambiente;
- commit ou tag;
- pipeline/artifact;
- resultado;
- limitação.

Print isolado não fecha requisito.

## 9. Abrir o Pull Request

O PR deve conter:

- objetivo e Issue;
- mudanças realizadas;
- critérios de aceite marcados;
- testes executados;
- evidências;
- impacto de segurança;
- migrations e compatibilidade;
- riscos/limitações;
- checklist de commits sem squash.

O autor não aprova o próprio item crítico.

## 10. Tratar a revisão

- Responder cada comentário.
- Fazer correções em novos commits pequenos.
- Não esconder correções com amend ou force push.
- Rodar novamente a suíte completa.
- Atualizar evidências quando o commit mudar.
- Solicitar nova revisão quando houver alteração relevante.

## 11. Mover para Done

A tarefa somente entra em Done quando:

- critérios de aceite estão comprovados;
- testes aplicáveis estão verdes;
- pipeline e gates passam;
- outro integrante aprovou;
- commits foram preservados;
- RTM, riscos, defeitos e documentos estão atualizados;
- evidência corresponde ao commit integrado;
- merge ocorreu sem squash.

Issue criada, código manualmente demonstrado ou teste sem execução não significa Done.

## 12. Defeitos encontrados

1. Abrir relato reproduzível e sanitizado.
2. Separar severidade de prioridade.
3. Reproduzir em teste vermelho.
4. Investigar causa raiz.
5. Corrigir a causa.
6. Executar regressão e suíte.
7. Atualizar RTM e processo.
8. Fechar somente com evidência.

## 13. Segurança antes de finalizar

- Nenhum segredo no código, configuração, fixture, log ou artifact.
- Política nega por padrão.
- Operação negada não altera o banco.
- Resposta não expõe stack trace, SQL, token ou dado interno.
- Alertas críticos são corrigidos ou bloqueiam a release.
- Zero bug ou vulnerabilidade crítica confirmada e aberta.

## 14. Responsabilidade dos quatro integrantes

Cada integrante precisa:

- implementar código Java relevante;
- criar testes relevantes;
- produzir commits próprios;
- abrir Pull Requests;
- revisar trabalho de outra pessoa;
- atualizar rastreabilidade;
- saber explicar arquitetura, segurança, concorrência, CI e evidências.

A divisão não será feita apenas por quantidade de Issues. Serão considerados pontos, dependências, risco e criticidade.
