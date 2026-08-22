# Quickstart: Validation Guide

**Feature**: Radar de Problemas | **Date**: 2026-08-22

## Overview

Guia passo-a-passo para validar que a feature **Radar de Problemas** funciona end-to-end conforme esperado. Este documento é *seu* roteiro de testes manuais após implementação.

## Prerequisites

- Python 3.11+ instalado
- Repositório clonado em diretório local
- Terminal aberto na raiz do projeto

## Setup

```bash
# 1. Criar ambiente virtual
python -m venv venv

# 2. Ativar ambiente (Windows)
venv\Scripts\activate
# ou (macOS/Linux)
source venv/bin/activate

# 3. Instalar dependências
pip install -r requirements.txt
```

## Test Scenario 1: Cadastro com Validação Correta

**Objetivo**: Verificar que um usuário consegue cadastrar um problema válido com sucesso.

### Steps

1. **Iniciar aplicação**:
   ```bash
   streamlit run src/app.py
   ```
   Aplicação deve abrir em `http://localhost:8501`

2. **Preencher formulário válido**:
   - Título: "Falta de integração com ferramentas de BI"
   - Descrição: "Empresas precisam exportar dados manualmente para análise em planilhas"
   - Público Afetado: "Analistas de dados em PMEs"
   - Frequência: 4
   - Intensidade: 5
   - Acesso: 3
   - Disposição a Pagar: 4

3. **Clicar "Salvar Problema"**

4. **Esperado**:
   - Mensagem de sucesso: "Problema cadastrado com sucesso!"
   - Problema aparece no ranking com score 4.00
   - Aplicação não trava

### Validation

✓ Problema foi persistido em `data/problems.json`  
✓ Score calculado corretamente: (4+5+3+4)/4 = 4.00

---

## Test Scenario 2: Validação de Título Vazio

**Objetivo**: Verificar que o sistema rejeita cadastro sem título com mensagem específica.

### Steps

1. **Preencher formulário, mas deixar Título vazio**:
   - Título: (deixar vazio)
   - Descrição: "Qualquer descrição"
   - Público: "Qualquer público"
   - Notas: (qualquer valores 1-5)

2. **Clicar "Salvar Problema"**

3. **Esperado**:
   - Erro exibido: "Erro: título é obrigatório"
   - Problema NÃO é salvo
   - Formulário permanece preenchido para correção

### Validation

✓ Nenhum novo problema em `data/problems.json`  
✓ Mensagem de erro é clara e específica

---

## Test Scenario 3: Validação de Nota Fora de Range

**Objetivo**: Verificar que o sistema rejeita notas fora de 1-5 com mensagem específica.

### Steps

1. **Preencher formulário com nota inválida**:
   - Título: "Teste"
   - Descrição: "Teste"
   - Público: "Teste"
   - Frequência: 6 (INVÁLIDO)
   - Outras notas: 3

2. **Clicar "Salvar Problema"**

3. **Esperado**:
   - Erro exibido: "Erro: notas devem estar entre 1 e 5"
   - Problema NÃO é salvo

### Validation

✓ Nenhum novo problema em `data/problems.json`

---

## Test Scenario 4: Ranking Ordenado e Destaque

**Objetivo**: Verificar que o ranking é exibido em ordem decrescente por score com destaque nos 3 primeiros.

### Setup

Cadastrar 5 problemas com os seguintes scores (via Test Scenario 1):

| Problema | Notas | Score |
|----------|-------|-------|
| P1 | 5,5,5,5 | 5.00 |
| P2 | 4,3,2,1 | 2.50 |
| P3 | 3,3,3,3 | 3.00 |
| P4 | 4,4,4,4 | 4.00 |
| P5 | 1,1,1,1 | 1.00 |

### Steps

1. **Abrir aplicação** (dados já existem)
2. **Visualizar ranking**

3. **Esperado**:
   - Ordem: P1 (5.00), P4 (4.00), P3 (3.00), P2 (2.50), P5 (1.00)
   - Três primeiros têm destaque visual claro (ex: fundo colorido, badge "🥇", ou similar)
   - Scores formatados com exatamente 2 casas decimais

### Validation

✓ Ordem decrescente  
✓ Destaque visual nos 3 primeiros  
✓ Formatação de score: "5.00", não "5"

---

## Test Scenario 5: Persistência (Fechamento e Reabertura)

**Objetivo**: Verificar que dados persistem entre execuções.

### Steps

1. **Cadastrar 2-3 problemas** (usando Test Scenario 1)
2. **Fechar aplicação**: Pressionar Ctrl+C no terminal
3. **Reabrir aplicação**: 
   ```bash
   streamlit run src/app.py
   ```
4. **Esperado**:
   - Todos os problemas aparecem no ranking
   - Scores estão corretos
   - Ordem está preservada

### Validation

✓ Arquivo `data/problems.json` contém todos os problemas  
✓ Nenhuma perda de dados

---

## Test Scenario 6: Arquivo JSON Corrompido (Edge Case)

**Objetivo**: Verificar que o sistema reage graciosamente a um arquivo JSON inválido.

### Steps

1. **Parar aplicação** (Ctrl+C)
2. **Editar `data/problems.json`** manualmente, introduzindo um erro de sintaxe:
   ```json
   {
     "version": "1.0",
     "problemas": [
       { "id": "test", ...invalid json
   ```
3. **Reabrir aplicação**
   ```bash
   streamlit run src/app.py
   ```

4. **Esperado**:
   - Aplicação NÃO trava
   - Mensagem de aviso exibida: "Aviso: arquivo de dados corrompido, iniciando com lista vazia"
   - Ranking exibido vazio ou com mensagem "Nenhum problema cadastrado"
   - Usuário consegue cadastrar novo problema normalmente

### Validation

✓ Aplicação é robusta a corruptions  
✓ Usuário consegue se recuperar (novo cadastro limpa estado)

---

## Test Scenario 7: Exportação CSV

**Objetivo**: Verificar que a exportação para CSV funciona e abre corretamente em planilhas.

### Steps

1. **Cadastrar 2 problemas** (usando Test Scenario 1)
2. **Clicar botão "Exportar para CSV"**
3. **Arquivo é baixado** (ex: `problemas.csv`)
4. **Abrir arquivo em Excel, Google Sheets ou editor de texto**

5. **Esperado**:
   - Colunas: título, descrição, público, frequência, intensidade, acesso, disposição_pagar, score
   - 2 linhas de dados + 1 linha de cabeçalho
   - Scores exibidos com 2 casas decimais
   - Caracteres especiais/UTF-8 preservados

### Validation

✓ Arquivo CSV aberto sem erros  
✓ Todas as colunas presentes  
✓ Dados corretos e completos

---

## Test Scenario 8: Testes Unitários (Domain Logic)

**Objetivo**: Verificar que regras de negócio estão cobertas por testes pytest.

### Steps

```bash
# Rodar todos os testes unitários
pytest tests/unit/ -v

# Rodar testes específicos de score
pytest tests/unit/test_services.py::test_calculate_score -v

# Rodar testes de validação
pytest tests/unit/test_services.py::test_validate_problem -v
```

### Esperado

- Todos os testes passam (exit code 0)
- Cobertura mínima: services.py 100%, storage.py 80%, models.py 100%
- Output mostra:
  ```
  test_calculate_score[5-5-5-5-5.00] PASSED
  test_calculate_score[4-3-2-1-2.50] PASSED
  test_validate_problem[titulo_vazio] PASSED
  test_validate_problem[nota_invalida] PASSED
  ...
  ```

### Validation

✓ Todos os testes passam  
✓ Cobertura adequada  
✓ pytest é instalado e funciona

---

## Summary Checklist

Após completar todos os 8 cenários acima, o feature deve estar **validado e pronto para produção**:

- [ ] Scenario 1: Cadastro válido funciona
- [ ] Scenario 2: Validação de título vazio funciona
- [ ] Scenario 3: Validação de notas fora de range funciona
- [ ] Scenario 4: Ranking ordenado e destaque dos 3 primeiros
- [ ] Scenario 5: Persistência entre execuções
- [ ] Scenario 6: Robustez a corruptions
- [ ] Scenario 7: Exportação CSV funciona
- [ ] Scenario 8: Testes unitários passam

## Troubleshooting

| Problema | Causa Provável | Solução |
|----------|----------------|---------|
| "ModuleNotFoundError: streamlit" | Dependências não instaladas | `pip install -r requirements.txt` |
| Aplicação trava ao abrir | Arquivo JSON corrompido | Deletar `data/problems.json` e reiniciar |
| Scores exibem com 1 casa decimal | Formatação incorreta | Verificar `calculate_score()` em `services.py` |
| CSV aberto com valores misterio | Encoding issue | Garantir UTF-8 no `storage.py` |

## Next Steps

Após passar em todos os cenários:
1. Rodar `/speckit-tasks` para gerar tasks de implementação detalhadas
2. Executar implementação seguindo o tasks.md
3. Rodar este quickstart novamente para validação final
