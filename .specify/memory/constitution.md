<!--
Sync Impact Report:
- Version change: scaffold/unversioned -> 1.0.0
- Modified principles: template placeholders -> all five project principles
- Added sections: Restrições Técnicas; Fluxo de Desenvolvimento e Qualidade
- Removed sections: none
- Follow-up TODOs: definir a data original de ratificação
-->
# Projeto Educacional em Python Constitution

## Core Principles

### I. Python Atual e Simplicidade
O projeto MUST executar em Python 3.11 ou superior e MUST preferir soluções simples,
legíveis e adequadas ao escopo educacional. Dependências e abstrações só podem ser
introduzidas quando reduzirem complexidade real ou atenderem a um requisito explícito.

### II. Interface Streamlit
A interface do usuário MUST ser implementada com Streamlit. A camada de interface MUST
coordenar entrada, saída e estado visual, sem concentrar regras de negócio ou cálculos de
score que pertençam ao domínio.

### III. Dados Locais e Autonomia
Os dados persistentes MUST ser armazenados em arquivo JSON local. O projeto MUST NOT
depender de serviços externos, APIs remotas, autenticação externa ou banco de dados para
executar suas funcionalidades principais.

### IV. Domínio Separado e Testável
As regras de negócio, incluindo score e validação, MUST ficar em módulos independentes da
interface Streamlit. Esses módulos MUST poder ser exercitados sem iniciar a aplicação nem
ler componentes visuais, mantendo entradas e saídas explícitas.

### V. Qualidade por Testes
As regras de score e validação MUST ser cobertas por testes automatizados com pytest. Toda
alteração nessas regras MUST incluir ou atualizar testes que cubram o comportamento válido,
os limites relevantes e as entradas inválidas. Os testes MUST ser determinísticos e locais.

## Restrições Técnicas

O código MUST seguir um estilo claro e consistente com a biblioteca padrão e as convenções
do projeto. Comentários devem ser escritos em português quando forem necessários para
explicar uma decisão ou regra não óbvia; o código deve permanecer autoexplicativo sempre
que possível. Arquivos JSON MUST ter formato válido e tratamento explícito para ausência,
corrupção ou dados incompatíveis.

## Fluxo de Desenvolvimento e Qualidade

Cada mudança MUST preservar a separação entre domínio, persistência e interface. Antes de
ser considerada concluída, a mudança MUST passar pelos testes pytest disponíveis; mudanças
na interface também devem ser verificadas por uma execução local do aplicativo quando isso
for viável. Revisões MUST confirmar compatibilidade com Python 3.11+, funcionamento sem
serviços externos e cobertura das regras de negócio afetadas.

## Governance

Esta constituição prevalece sobre práticas conflitantes do projeto. Alterações devem ser
registradas neste arquivo, atualizar o relatório de impacto e ajustar a versão conforme
as regras abaixo. Uma remoção ou redefinição incompatível de princípio exige versão MAJOR;
um novo princípio ou expansão material exige MINOR; correções e esclarecimentos sem mudança
semântica exigem PATCH. Toda revisão de código MUST verificar conformidade com os princípios
e justificar qualquer exceção no próprio contexto da mudança.

**Version**: 1.0.0 | **Ratified**: TODO(RATIFICATION_DATE): informar a data original de adoção | **Last Amended**: 2026-08-19
