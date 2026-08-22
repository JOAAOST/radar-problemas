# Feature Specification: Radar de Problemas

**Feature Branch**: `001-problema-radar`

**Created**: 2026-08-22

**Status**: Draft

**Input**: User description: "O aplicativo 'Radar de Problemas' é uma ferramenta para registrar, avaliar e priorizar dores de mercado e oportunidades com critérios rígidos de entrada, cálculo de score, ranking visual e exportação."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Cadastro de Problema (Priority: P1)

Um usuário do mercado precisa registrar um problema/oportunidade que identificou. Ele insere título, descrição (qual o problema), público afetado (quem sofre) e quatro notas obrigatórias de 1 a 5 para medir frequência da dor, intensidade da dor, facilidade de acesso aos usuários e disposição a pagar. O sistema valida cada campo, rejeita entradas inválidas com mensagem clara e confirma o cadastro ao usuário.

**Why this priority**: Sem cadastro, não há dados para o sistema; é o requisito fundador.

**Independent Test**: Pode ser testado isoladamente: usuário consegue cadastrar um problema válido e recebe confirmação; tentativas inválidas (sem título ou notas fora de 1-5) são rejeitadas com mensagem específica.

**Acceptance Scenarios**:

1. **Given** interface de cadastro vazia, **When** usuário insere título, descrição, público e 4 notas (1-5) e clica "Salvar", **Then** problema é armazenado e mensagem de sucesso é exibida.
2. **Given** interface de cadastro, **When** usuário tenta salvar sem título, **Then** sistema exibe "Erro: título é obrigatório" e não salva.
3. **Given** interface de cadastro, **When** usuário insere nota fora de 1-5 (ex: 0, 6, ou texto), **Then** sistema exibe "Erro: notas devem estar entre 1 e 5" e não salva.
4. **Given** interface de cadastro, **When** usuário insere todos os campos corretamente, **Then** problema é salvo com sucesso imediato.

---

### User Story 2 - Visualizar Ranking Priorizado (Priority: P1)

Um analista de mercado abre o aplicativo e precisa ver todos os problemas cadastrados em ordem de importância. O sistema exibe uma lista classificada de forma decrescente pelo score (média das 4 notas, com 2 casas decimais). Os 3 primeiros colocados recebem destaque visual claro (cor, ícone ou outro indicador) para identificação imediata das oportunidades mais críticas.

**Why this priority**: O ranking é o valor central do aplicativo; sem ele, as notas isoladas não ajudam na priorização.

**Independent Test**: Pode ser testado com dados pré-carregados: lista é exibida em ordem decrescente por score, 3 primeiros têm destaque, scores são calculados corretamente.

**Acceptance Scenarios**:

1. **Given** aplicativo com 5 problemas cadastrados (scores: 4.5, 3.2, 4.8, 2.1, 4.0), **When** usuário visualiza a lista, **Then** ordem é 4.8, 4.5, 4.0, 3.2, 2.1 e os 3 primeiros têm destaque visual.
2. **Given** lista de problemas, **When** usuário vê um score exibido, **Then** valor tem exatamente 2 casas decimais (ex: "4.50", não "4.5").
3. **Given** lista de problemas, **When** usuário identifica visualmente os 3 primeiros, **Then** destaque é inequívoco (cor, badge ou similar).

---

### User Story 3 - Persistência de Dados (Priority: P1)

Um usuário cadastra problemas, fecha o aplicativo e o reabre. Todos os problemas continuam disponíveis no mesmo estado, com os mesmos scores e ranking.

**Why this priority**: Sem persistência, usuário perde dados; confiança na ferramenta desaparece.

**Independent Test**: Pode ser testado em isolation: salvar dados, encerrar aplicativo, reiniciar e verificar que dados estão lá.

**Acceptance Scenarios**:

1. **Given** aplicativo com 3 problemas cadastrados, **When** usuário fecha e reabre aplicativo, **Then** todos os 3 problemas continuam visíveis com mesmos scores e ranking.
2. **Given** aplicativo recém-iniciado e arquivo JSON vazio/ausente, **When** usuário visualiza lista, **Then** aplicativo não falha; exibe lista vazia ou mensagem "Nenhum problema cadastrado".

---

### User Story 4 - Exportar para CSV (Priority: P2)

Um analista precisa compartilhar a lista de problemas com o time em um formato que abra em planilhas (Excel, Google Sheets, etc.). Com um clique, o sistema exporta todos os problemas para um arquivo CSV.

**Why this priority**: Aumenta usabilidade e integração com fluxos existentes; não bloqueia o core do app.

**Independent Test**: Pode ser testado isoladamente: clicar botão de exportação gera arquivo CSV válido com dados corretos.

**Acceptance Scenarios**:

1. **Given** aplicativo com problemas cadastrados, **When** usuário clica "Exportar para CSV", **Then** arquivo é baixado/salvo com sucesso.
2. **Given** arquivo CSV exportado, **When** aberto em um editor de planilhas, **Then** colunas são: título, descrição, público, frequência, intensidade, acesso, disposição_pagar, score.
3. **Given** aplicativo vazio, **When** usuário clica "Exportar para CSV", **Then** arquivo CSV é criado com apenas cabeçalhos (sem erro).

---

### Edge Cases

- O que acontece se o arquivo JSON está corrompido ao iniciar o aplicativo?
- Como o sistema reage se usuário fecha/abre aplicativo durante cadastro?
- O que ocorre se todas as 4 notas são iguais (ex: 5, 5, 5, 5)? Score deve ser 5.00.
- É possível editar ou deletar um problema cadastrado? (Especificação: não, fora do escopo. Apenas criar.)

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Sistema MUST aceitar cadastro de problema com cinco campos obrigatórios: título (texto), descrição (texto), público afetado (texto), frequência (1-5), intensidade (1-5), acesso (1-5), disposição_pagar (1-5).
- **FR-002**: Sistema MUST calcular score como a média aritmética simples das 4 notas, formatado com exatamente 2 casas decimais (ex: "4.50", "3.20").
- **FR-003**: Sistema MUST exibir lista de todos os problemas cadastrados.
- **FR-004**: Sistema MUST ordenar lista de forma decrescente pelo score (maior primeiro).
- **FR-005**: Sistema MUST aplicar destaque visual claro (cor, badge, ou ícone) aos 3 primeiros problemas do ranking (quando houver 3 ou mais).
- **FR-006**: Sistema MUST persistir dados em arquivo JSON local, carregando-os automaticamente ao iniciar.
- **FR-007**: Sistema MUST exportar a lista completa para arquivo CSV com um clique, incluindo todas as colunas (título, descrição, público, 4 notas, score).
- **FR-008**: Sistema MUST validar entrada e rejeitar imediatamente qualquer tentativa de cadastro com título vazio.
- **FR-009**: Sistema MUST validar entrada e rejeitar imediatamente qualquer tentativa com notas fora do intervalo 1-5.
- **FR-010**: Sistema MUST exibir mensagem de erro clara e descritiva na tela para cada rejeição (sem título: "Título é obrigatório"; nota inválida: "Notas devem estar entre 1 e 5").
- **FR-011**: Sistema MUST lidar graciosamente com arquivo JSON ausente ou corrompido ao iniciar (carregar estado vazio ou exibir mensagem orientadora).

### Key Entities

- **Problema**: Representa uma dor/oportunidade de mercado. Atributos: id único, título, descrição, público_afetado, frequencia_nota (1-5), intensidade_nota (1-5), acesso_nota (1-5), disposicao_pagar_nota (1-5), score (calculado). Sem suporte para edição ou exclusão nesta versão.
- **ArquivoDados**: Contêiner JSON persistente que armazena lista de Problemas.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Usuário consegue cadastrar um problema válido em menos de 1 minuto a partir da primeira execução (interface clara e intuitiva).
- **SC-002**: Sistema exibe ranking completo ordenado e formatado em até 1 segundo (resposta rápida após abertura ou cadastro).
- **SC-003**: 100% das tentativas de cadastro inválido (sem título ou nota fora de 1-5) são rejeitadas com mensagem específica correspondente ao erro.
- **SC-004**: Dados persistem perfeitamente entre execuções (zero perda, zero corrupção em ciclos normais de uso).
- **SC-005**: Arquivo CSV exportado abre corretamente em Excel, Google Sheets ou editor de texto sem erros de formatação.

## Assumptions

- Interface é executada localmente por um único usuário por vez (sem concorrência multi-usuário).
- JSON é o único formato de armazenamento persistente; não há sincronização, backup automático ou versionamento.
- Streamlit é a tecnologia escolhida para interface (conforme Constituição do Projeto).
- Usuários têm acesso ao sistema de arquivos local para ler/escrever JSON e exportar CSV.
- Escopo explicitamente EXCLUI: edição e exclusão de problemas, autenticação, multi-usuário, replicação de dados, UI mobile.
- Scores são calculados apenas sob demanda (visualização de lista) e são exibidos com a mesma fórmula: (nota1 + nota2 + nota3 + nota4) / 4, arredondado para 2 casas decimais.
- Caracteres especiais em título, descrição e público devem ser tratados normalmente (UTF-8 permitido).
