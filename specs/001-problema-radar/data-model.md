# Phase 1: Data Model

**Feature**: Radar de Problemas | **Date**: 2026-08-22

## Entity: Problema

Representa uma dor/oportunidade de mercado.

### Attributes

| Atributo | Tipo | Obrigatório | Descrição | Validação |
|----------|------|------------|-----------|-----------|
| `id` | string (UUID) | Sim | Identificador único do problema | Auto-gerado ao criar |
| `titulo` | string | Sim | Título do problema | Não vazio, max 200 caracteres |
| `descricao` | string | Sim | Descrição detalhada | Não vazio, max 2000 caracteres |
| `publico_afetado` | string | Sim | Descrição do público afetado | Não vazio, max 200 caracteres |
| `frequencia_nota` | int | Sim | Frequência da dor (1-5) | >= 1 e <= 5 |
| `intensidade_nota` | int | Sim | Intensidade da dor (1-5) | >= 1 e <= 5 |
| `acesso_nota` | int | Sim | Facilidade de acesso aos usuários (1-5) | >= 1 e <= 5 |
| `disposicao_pagar_nota` | int | Sim | Disposição a pagar (1-5) | >= 1 e <= 5 |
| `score` | float | Calculado | Score médio (2 casas decimais) | (frequencia + intensidade + acesso + disposição) / 4, formatado com .2f |
| `criado_em` | datetime | Sim | Timestamp de criação | Auto-gerado ao criar |

### Relationships

Nenhuma relação com outras entidades nesta versão.

### State Transitions

```
CREATED → (persistido em JSON)
DELETED → (via interface futura, fora do escopo v1)
UPDATED → (via interface futura, fora do escopo v1)
```

**Nota**: V1 suporta apenas CREATE. Edição e exclusão estão fora do escopo.

### Constraints

- Não pode haver dois problemas com o mesmo ID
- Score é calculado dinamicamente a partir das 4 notas (não armazenado redundante, mas pode ser cacheado para display)
- Todos os campos de texto suportam UTF-8

## Entity: ArquivoDados (File-based Storage Contract)

Representação do arquivo JSON de persistência.

### Structure

```json
{
  "version": "1.0",
  "problemas": [
    {
      "id": "uuid-string",
      "titulo": "...",
      "descricao": "...",
      "publico_afetado": "...",
      "frequencia_nota": 4,
      "intensidade_nota": 5,
      "acesso_nota": 3,
      "disposicao_pagar_nota": 2,
      "criado_em": "2026-08-22T10:30:00Z"
    }
  ]
}
```

### Persistence Contract

- **Arquivo**: `data/problems.json` no diretório de trabalho
- **Codificação**: UTF-8
- **Formato**: JSON válido
- **Carregamento**: Ao iniciar, tentar ler; se ausente/corrompido, iniciar vazio com aviso
- **Salvamento**: Síncrono após cada novo problema; sobrescrever arquivo completo

### Validation Rules

Todas as regras estão codificadas no módulo `services.py`, não no schema JSON. O JSON é apenas container.

## Relationships & Dependencies

```
Problema (entity) → services.validate() → boolean
Problema (entity) → services.calculate_score() → float
ArquivoDados (container) → storage.load_problems() → List[Problema]
ArquivoDados (container) → storage.save_problems() → void
```

## Summary

Model é simples: uma única entidade `Problema`, sem relacionamentos. Toda a validação e lógica de score é centralizada em `services.py`, permitindo testes independentes conforme Princípio IV da Constituição.
