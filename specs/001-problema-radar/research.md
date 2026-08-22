# Phase 0: Research & Findings

**Feature**: Radar de Problemas | **Date**: 2026-08-22

## Overview

Especificação completamente clara; nenhum NEEDS CLARIFICATION marcado. Esta seção consolida as decisões técnicas de pesquisa já validadas pela especificação e confirmadas em alinhamento com a constituição do projeto.

## Decisions Made

### Decision: Python 3.11+ com Streamlit

**Choice**: Usar Python 3.11+ e Streamlit para interface

**Rationale**: 
- Obrigatório pela Constituição (Princípio II)
- Streamlit é a escolha natural para prototipagem rápida de aplicações de dados
- Python 3.11+ oferece performance adequada e biblioteca padrão madura

**Alternatives Considered**: 
- FastAPI + React: descartado por complexidade desnecessária para single-user local
- Flask + Jinja2: descartado por menos integração nativa com visualização de dados

### Decision: JSON para Persistência

**Choice**: Arquivo JSON local (sem banco de dados)

**Rationale**:
- Obrigatório pela Constituição (Princípio III)
- Escopo de single-user permite simplicidade máxima
- JSON é formato padrão, legível e sem dependências externas
- Adequado para centenas de problemas (escala do feature)

**Alternatives Considered**:
- SQLite: descartado por necessidade de driver externo; JSON suficiente para escala
- Cloud storage: descartado por restrição de autonomia local

### Decision: pytest para Testes

**Choice**: pytest como framework de testes principal

**Rationale**:
- Obrigatório pela Constituição (Princípio V)
- pytest é padrão em Python, simples e poderoso
- Suporta fixtures, parametrização, e integração com CI/CD

**Alternatives Considered**:
- unittest: descartado por menos expressividade e menos conveniente
- doctest: descartado por insuficiente para validação rigorosa de regras

### Decision: Separação Domínio / Interface

**Choice**: Módulos `services.py`, `models.py`, `storage.py` separados de `app.py`

**Rationale**:
- Obrigatório pela Constituição (Princípio IV)
- Permite testes do score() e validate() sem iniciar Streamlit
- Manutenibilidade e reutilização futura

**Alternatives Considered**:
- Lógica embedding na app.py: descartado por violar testabilidade e princípios do projeto

## Summary

Todas as decisões técnicas estão alinhadas com a Constituição do Projeto e com as melhores práticas para o escopo definido. Nenhuma pesquisa adicional necessária; ready for Phase 1 design.
