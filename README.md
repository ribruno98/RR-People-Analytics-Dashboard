# RR-People-Analytics-Dashboard

Este repositório contém um projeto completo de **People Analytics** desenvolvido no **Power BI**, focado em analisar o perfil demográfico, a estrutura salarial e os fatores críticos de rotatividade (churn) de uma organização. 

O objetivo principal deste painel é transformar dados brutos de Recursos Humanos em insights estratégicos, permitindo que a liderança tome decisões informadas para reduzir a perda de talentos, otimizar a folha salarial e garantir a equidade dentro da empresa.

---

## Contexto do Problema de Negócio
A retenção de talentos e a gestão eficiente da massa salarial são dois dos maiores desafios enfrentados pelos departamentos de Recursos Humanos modernos. Uma taxa de rotatividade elevada gera custos ocultos massivos (recrutamento, integração, perda de produtividade e conhecimento técnico). 

Este painel foi desenhado para responder a três perguntas-chave da diretoria:
1. **Quanto custa a nossa estrutura atual** e como o orçamento está distribuído entre os departamentos?
2. **Existe equidade e justiça salarial** dentro da organização quando analisamos os géneros?
3. **Onde e por que razão estamos a perder colaboradores?** Quais são os perfis mais propensos a deixar a empresa?

---

## Tecnologias e Competências Utilizadas
* **Mockaroo:** Engenharia e geração de dados sintéticos realistas baseados em regras de negócio.
* **Power BI Desktop:** Ligação de dados, modelação, criação de relatórios e design de interface (UI/UX).
* **Power Query (M):** Tipagem de dados, tratamento de colunas e criação de dimensões personalizadas.
* **DAX (Data Analysis Expressions):** Desenvolvimento de métricas de negócio customizadas e inteligência de tempo.
* **Modelação Dimensional:** Implementação de uma arquitetura robusta em estrela (*Star Schema*).

---

## Modelação de Dados (Star Schema)
Para garantir a máxima performance das fórmulas e uma estrutura limpa, o modelo foi desenhado utilizando o conceito de **Star Schema** (Tabelas de Factos e Tabelas de Dimensão):

* **Tabela de Factos Central:**
  * `Classificação_Desempenho`: Regista o histórico de avaliações anuais, notas dos gestores, autoavaliações e níveis de satisfação ao longo do tempo.
* **Tabelas de Dimensão:**
  * `Funcionários` *(DimEmployee)*: Cadastro fixo dos colaboradores, departamentos, género, data de contratação, salário e indicador de demissão (*attrition*).
  * `dCalendario`: Tabela de tempo para análises temporais e tendências mensais/anuais.
  * `d_Escolaridade`: Tabela de suporte para tradução dos níveis de estudos.
  * `d_Satisfação`: Tabela de suporte para categorização da satisfação do colaborador.
  * `d_Classificação`: Tabela de suporte para as notas de desempenho.

*Os relacionamentos foram configurados com cardinalidade **1 para Muitos (1 -> *)** e direção de filtro único, garantindo a correta filtragem e integridade do modelo.*

<img width="1121" height="797" alt="Captura de ecrã 2026-06-04 164217" src="https://github.com/user-attachments/assets/26719918-f2c6-4c3c-b06a-f7c83c64a158" />


---

## Métricas de Negócio Desenvolvidas (DAX)
Todas as métricas foram criadas explicitamente numa tabela dedicada de medidas (`_Medidas`) para garantir a organização do projeto:

```dax
// 1. Total de Contratações Históricas
Total Contratações = COUNT(Funcionários[id_funcionario])

// 2. Colaboradores Ativos Atualmente na Organização
Colaboradores Ativos = 
CALCULATE(
    COUNT(Funcionários[id_funcionario]), 
    Funcionários[attrition] = "Não"
)

// 3. Total de Demissões (Colaboradores que saíram)
Total Demissões = 
CALCULATE(
    COUNT(Funcionários[id_funcionario]), 
    Funcionários[attrition] = "Sim"
)

// 4. Taxa de Churn / Rotatividade (Métrica Crítica de RH)
Taxa de Churn = DIVIDE([Total Demissões], [Total Contratações], 0)

// 5. Massa Salarial (Custo total anual com pessoal)
Massa Salarial = SUM(Funcionários[salario])

// 6. Média Salarial dos Colaboradores
Média Salarial = AVERAGE(Funcionários[salario])

---

