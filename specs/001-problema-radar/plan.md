# Implementation Plan: Radar de Problemas

**Branch**: `001-problema-radar` | **Date**: 2026-08-22 | **Spec**: [spec.md](spec.md)

**Input**: Feature specification from `/specs/001-problema-radar/spec.md`

## Summary

Construir uma aplicação web em Streamlit (Python 3.11+) que permite o cadastro, avaliação e priorização de problemas/oportunidades de mercado. O usuário insere título, descrição, público afetado e quatro notas (1-5), o sistema calcula o score (média simples) e exibe um ranking ordenado com destaque visual para os 3 primeiros. Dados persistem em JSON local; exportação para CSV com um clique. Validação rígida de entrada e módulos de regras de negócio separados da interface para testabilidade.

## Technical Context

**Language/Version**: Python 3.11+

**Primary Dependencies**: Streamlit, pytest (testing)

**Storage**: JSON file (local, single-file data store)

**Testing**: pytest with unit tests for domain logic

**Target Platform**: Local desktop/web (via Streamlit)

**Project Type**: web application (single-user, Streamlit-based)

**Performance Goals**: Ranking display in <1 second; CSV export in <2 seconds; form submission response <500ms

**Constraints**: No external services, no database, no authentication, local-only execution, offline-capable

**Scale/Scope**: Single user at a time, hundreds of problems maximum, 7 screens (form, ranking, export confirmation, error states)

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

✓ **Princípio I** (Python 3.11+ e Simplicidade): Usando Python 3.11+, arquitetura simples, sem abstrações desnecessárias.

✓ **Princípio II** (Interface Streamlit): Interface implementada em Streamlit conforme requisito.

✓ **Princípio III** (Dados Locais): JSON local, sem serviços externos, sem BD, sem APIs remotas.

✓ **Princípio IV** (Domínio Separado): Módulos `models.py` (entidades), `services.py` (score, validação), `storage.py` (persistência) isolados de `app.py` (Streamlit). Totalmente testável sem UI.

✓ **Princípio V** (Qualidade por Testes): Testes pytest cobrindo score, validação, persistência; módulos de domínio exercitáveis em isolamento.

**GATE STATUS**: ✓ PASS — Nenhuma violação de constituição.

## Project Structure

### Documentation (this feature)

```text
specs/[###-feature]/
├── plan.md              # This file (/speckit-plan command output)
├── research.md          # Phase 0 output (/speckit-plan command)
├── data-model.md        # Phase 1 output (/speckit-plan command)
├── quickstart.md        # Phase 1 output (/speckit-plan command)
├── contracts/           # Phase 1 output (/speckit-plan command)
└── tasks.md             # Phase 2 output (/speckit-tasks command - NOT created by /speckit-plan)
```

### Source Code (repository root)

```text
src/
├── models.py           # Problema entity, dataclass, domain types
├── services.py         # score(), validate() business logic
├── storage.py          # load_problems(), save_problems(), JSON I/O
├── app.py              # Streamlit UI, main()
└── __init__.py

tests/
├── unit/
│   ├── test_services.py       # score, validação
│   ├── test_storage.py        # JSON load/save, error handling
│   └── test_models.py         # entity structure, defaults
└── conftest.py                # pytest fixtures

data/
└── problems.json        # Runtime persistent data (auto-created)

requirements.txt        # streamlit, pytest
README.md              # Setup, usage, running tests
```

**Structure Decision**: Single-project layout (Option 1) com separação clara entre domínio (`services.py`, `models.py`, `storage.py`), interface (`app.py`) e testes (`tests/unit/`). Suporta pytest sem necessidade de instanciar Streamlit, alinhado com Princípio IV da constituição.

## Complexity Tracking

*No constitutional violations; tracking table omitted.*
