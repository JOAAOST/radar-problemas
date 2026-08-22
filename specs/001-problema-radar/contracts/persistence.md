# Contract: Persistence Interface

**Feature**: Radar de Problemas | **Date**: 2026-08-22

## Overview

Interface contratual para persistência de dados em JSON. Define o contrato entre a camada de interface (Streamlit) e o módulo `storage.py`.

## Function: load_problems()

### Signature

```python
def load_problems() -> List[Problema]
```

### Contract

**Input**: Nenhum. Lê do arquivo `data/problems.json`.

**Output**: Lista de objetos Problema; lista vazia se arquivo ausente ou vazio.

**Behavior**:

| Cenário | Comportamento Esperado |
|---------|----------------------|
| Arquivo JSON válido com 3 problemas | Retorna lista com 3 Problema |
| Arquivo ausente | Retorna [] (lista vazia), sem erro fatal |
| Arquivo corrompido (JSON inválido) | Log warning, retorna [] |
| Arquivo vazio | Retorna [] |
| Arquivo com "problemas": [] | Retorna [] |

**Side-effects**:
- Pode logar warnings para corruptions
- Não modifica arquivo durante leitura
- Cria diretório `data/` se não existir

## Function: save_problems()

### Signature

```python
def save_problems(problems: List[Problema]) -> bool
```

### Contract

**Input**: Lista de objetos Problema.

**Output**: True se salvamento bem-sucedido; False se falha (ex: permissão).

**Behavior**:

| Cenário | Resultado |
|---------|-----------|
| Lista vazia | Arquivo JSON criado com `{"version": "1.0", "problemas": []}` |
| Lista com 5 problemas | Arquivo sobrescrito com formato completo |
| Erro de disco/permissão | Retorna False, nenhuma mudança no arquivo anterior |
| Diretório `data/` não existe | Criar diretório antes de salvar |

**Format**:

Arquivo `data/problems.json`:
```json
{
  "version": "1.0",
  "problemas": [
    {
      "id": "...",
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

**Encoding**: UTF-8 com indentation (2 espaços para legibilidade).

## Summary

Contratos de persistência são simples e robustos: load é tolerante (retorna [] em erros), save é atômico (sobrescreve completo). Totalmente testável com fixtures em pytest.
