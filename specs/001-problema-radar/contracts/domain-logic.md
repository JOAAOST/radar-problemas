# Contract: Domain Logic Interface

**Feature**: Radar de Problemas | **Date**: 2026-08-22

## Overview

Interface contratual das regras de negócio (score, validação). Estes contratos definem o comportamento esperado dos módulos `services.py` e são totalmente testáveis via pytest sem dependência da UI Streamlit.

## Function: validate_problem()

### Signature

```python
def validate_problem(
    titulo: str,
    descricao: str,
    publico_afetado: str,
    frequencia_nota: int,
    intensidade_nota: int,
    acesso_nota: int,
    disposicao_pagar_nota: int
) -> tuple[bool, Optional[str]]
```

### Contract

**Input**: Sete strings/ints representando campos de um novo problema.

**Output**: Tupla `(is_valid: bool, error_message: Optional[str])`
- Se válido: `(True, None)`
- Se inválido: `(False, "mensagem de erro específica")`

**Rules**:

| Regra | Condição | Erro Esperado |
|-------|----------|---------------|
| Título obrigatório | titulo vazio ou None | "Erro: título é obrigatório" |
| Descrição obrigatória | descricao vazio ou None | "Erro: descrição é obrigatória" |
| Público obrigatório | publico_afetado vazio ou None | "Erro: público afetado é obrigatório" |
| Frequência in range | frequencia_nota not in [1,2,3,4,5] | "Erro: notas devem estar entre 1 e 5" |
| Intensidade in range | intensidade_nota not in [1,2,3,4,5] | "Erro: notas devem estar entre 1 e 5" |
| Acesso in range | acesso_nota not in [1,2,3,4,5] | "Erro: notas devem estar entre 1 e 5" |
| Disposição in range | disposicao_pagar_nota not in [1,2,3,4,5] | "Erro: notas devem estar entre 1 e 5" |

**Note**: Descrição e público_afetado podem ter limite de caracteres (conforme data-model.md), mas para v1 aceitam qualquer texto não-vazio.

## Function: calculate_score()

### Signature

```python
def calculate_score(
    frequencia: int,
    intensidade: int,
    acesso: int,
    disposicao: int
) -> float
```

### Contract

**Input**: Quatro inteiros entre 1 e 5.

**Output**: Float com exatamente 2 casas decimais (ex: 4.50, 3.20, 1.00).

**Formula**: `(frequencia + intensidade + acesso + disposicao) / 4`, arredondado para 2 casas decimais.

**Examples**:

| Entrada | Score Esperado |
|---------|----------------|
| (5,5,5,5) | 5.00 |
| (4,3,2,1) | 2.50 |
| (1,1,1,1) | 1.00 |
| (3,3,3,3) | 3.00 |

**Note**: Score é sempre calculado, nunca armazenado redundante (evita inconsistência).

## Function: sort_problems_by_score()

### Signature

```python
def sort_problems_by_score(problems: List[Problema]) -> List[Problema]
```

### Contract

**Input**: Lista de objetos Problema.

**Output**: Lista ordenada de forma decrescente pelo score (maior primeiro).

**Example**:

Input:
```
[
  Problema(id="p1", ..., frequencia=4, intensidade=3, acesso=2, disposicao=1),  # score 2.50
  Problema(id="p2", ..., frequencia=5, intensidade=5, acesso=5, disposicao=5),  # score 5.00
  Problema(id="p3", ..., frequencia=3, intensidade=3, acesso=3, disposicao=3),  # score 3.00
]
```

Output:
```
[
  Problema(id="p2", ..., score=5.00),
  Problema(id="p3", ..., score=3.00),
  Problema(id="p1", ..., score=2.50),
]
```

## Summary

Todos os contratos são determinísticos, sem side-effects, e totalmente testáveis via pytest fixtures. Nenhuma referência a Streamlit ou state visual.
