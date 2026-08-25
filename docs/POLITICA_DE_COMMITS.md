# Política obrigatória de commits

Esta política vale para todo o projeto **Gerenciador de Salas de Aula** e deve ser seguida pelos quatro integrantes.

## Regra principal

**Toda alteração concluída em qualquer arquivo deve gerar um commit próprio.**

Isso inclui alterações pequenas em:

- código Java;
- testes;
- configurações;
- migrations e banco de dados;
- documentação;
- diagramas;
- scripts;
- workflows do GitHub Actions;
- arquivos do JMeter;
- relatórios e evidências.

## Granularidade obrigatória

- Alterou um arquivo: faça o commit dessa alteração antes de iniciar a próxima.
- Alterou três arquivos: faça, no mínimo, três commits separados.
- Fez duas alterações independentes no mesmo arquivo: faça dois commits.
- Código, teste, documentação, configuração e evidência nunca devem ser agrupados no mesmo commit.
- Correções feitas após revisão devem gerar novos commits; não esconder a correção com `amend`.
- Não usar `git commit --amend`, rebase interativo para juntar commits ou merge com squash.
- O Pull Request deve preservar o histórico completo dos commits.
- Cada commit deve representar uma única alteração verificável e referenciar a Issue correspondente.

## Fluxo para cada alteração

1. Escolher uma Issue e mover para **In progress**.
2. Criar uma branch exclusiva a partir da `main` atualizada.
3. Fazer uma única alteração.
4. Executar o teste ou a verificação correspondente.
5. Adicionar somente o arquivo alterado ao stage.
6. Criar o commit imediatamente.
7. Repetir o processo para a próxima alteração.
8. Fazer push da branch e abrir Pull Request.
9. Solicitar revisão de outro integrante.
10. Corrigir cada apontamento em um novo commit.
11. Fazer merge sem squash depois do pipeline verde e da aprovação.

## Comandos de conferência

```bash
git status
git diff
git add caminho/do/arquivo
git diff --cached
git commit -m "tipo: descreve uma única alteração (#numero-da-issue)"
git log --oneline
```

Nunca usar `git add .` ou `git add -A` neste projeto. O caminho do arquivo deve ser informado explicitamente.

## Padrão das mensagens

```text
feat: adiciona entidade de sala (#15)
test: cobre capacidade inválida da sala (#15)
fix: impede reserva de sala em manutenção (#22)
docs: documenta fluxo de aprovação (#35)
ci: executa testes em pull requests (#52)
perf: adiciona cenário concorrente no JMeter (#54)
refactor: extrai validador de horários (#27)
```

## Autoria e revisão

- Cada integrante deve produzir commits importantes de código e de testes.
- Cada integrante deve abrir Pull Requests próprios.
- Cada Pull Request deve ser revisado por outro integrante.
- A autoria não pode ser simulada, compartilhada ou concentrada em uma única pessoa.
- Mudanças de quadro, atribuições e comentários em Issues são atividades do GitHub, mas não são commits. Decisões de planejamento que precisem de histórico devem também ser registradas em arquivo versionado.

## Checklist do Pull Request

- [ ] Cada alteração de arquivo possui commit próprio.
- [ ] Não há commits que misturam arquivos ou intenções diferentes.
- [ ] Os commits referenciam a Issue.
- [ ] Código e testes estão em commits separados.
- [ ] Documentação e RTM foram atualizadas em commits separados quando necessário.
- [ ] O pipeline está verde.
- [ ] Outro integrante revisou e aprovou.
- [ ] O merge será feito sem squash.
