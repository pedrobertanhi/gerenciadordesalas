# Registros de Decisão Arquitetural (ADR)

> Status: nenhuma decisão registrada.

## Convenção

Cada decisão deve usar um arquivo separado:

```text
0001-titulo-da-decisao.md
0002-titulo-da-decisao.md
```

## Modelo

```markdown
# ADR-NNNN: Título

- Status: proposta | aceita | substituída
- Data:
- Autores:
- Issue:

## Contexto

## Alternativas consideradas

## Decisão

## Consequências

## Evidências
```

## Decisões iniciais esperadas

- [ ] Arquitetura em camadas ou hexagonal.
- [ ] Estratégia de autenticação.
- [ ] Interface web e tecnologia de apresentação.
- [ ] Tratamento de data, hora e fuso.
- [ ] Controle transacional de concorrência.
- [ ] Estratégia de notificações.

Cada ADR deve ser criado em commit próprio. Uma decisão substituída permanece no histórico.
