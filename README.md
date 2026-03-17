<div align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/1/1a/Bras%C3%A3o_da_PUC_Minas.svg/1200px-Bras%C3%A3o_da_PUC_Minas.svg.png" width="200" alt="PUC Minas Logo">
  <h1>Pontifícia Universidade Católica de Minas Gerais</h1>
  <h3>Pós-graduação em DataOps e MLOps</h3>
  <p><strong>Disciplina:</strong> Integração e Entrega Contínuas (CI/CD)</p>
  <p><strong>Tutorial Prático:</strong> Construção de Esteira CI/CD</p>
</div>

---

## 🚀 Sobre o Projeto

Este projeto tem como objetivo demonstrar a construção prática e incremental de uma esteira robusta de **Continuous Integration (CI)** e **Continuous Delivery (CD)** utilizando **GitHub Actions**.

A partir de um pipeline de dados em Python que lê e processa um arquivo de vendas CSV, construímos a automação para garantir a qualidade do código antes da entrega do artefato final.

### 🛠️ Estrutura do Repositório

```text
ci-cd-lab/
├── .github/workflows/
│   └── ci.yml             # Workflow de CI/CD (GitHub Actions)
├── app/
│   └── pipeline.py        # Script Python de processamento de dados
├── data/
│   └── sales.csv          # Arquivo base de vendas
├── tests/
│   └── test_pipeline.py   # Testes automatizados (pytest)
├── README.md              # Este documento
└── requirements.txt       # Dependências do projeto
```

---

## ⚙️ A Esteira de CI/CD Implementada

O workflow contido em `.github/workflows/ci.yml` foi desenhado com dois _Jobs_ principais para segregar a validação do deploy, conforme boas práticas de Engenharia de Software.

### 1. Job: Validação (`validate`)
Executado tanto na branch `main` quanto nas branches `feature/esteira`. O objetivo é garantir a máxima confiabilidade do código através da nossa "régua de qualidade".

- **Checkout**: Baixa o código-fonte na máquina virtual (`ubuntu-latest`).
- **Setup Python**: Prepara o ambiente isolado com Python 3.11.
- **Linting (`flake8`)**: Valida o estilo do código (sintaxe e padronização visual).
- **Testes Unitários (`pytest`)**: Confirma o comportamento funcional da aplicação.
- **Tipagem Estática (`mypy`)**: Quality gate extra para validar assinaturas e anotações de tipo.
- **Geração de Artefato**: Se aprovado nas etapas anteriores, roda o gerador de resumos e salva o `output/summary.csv` como artefato preservado pelo Runner.

### 2. Job: Entrega Contínua (`deploy`)
Executado **apenas** quando o commit (ou merge) atinge a branch `main` e se, e somente se, o job de validação passar com sucesso (*`needs: validate`*).
Neste cenário simulado, o log acusa a promoção do artefato para a produção.

---

## 🧠 Reflexão Técnica (Entrega do Laboratório)

Conforme os requisitos acadêmicos da atividade, apresentamos as reflexões abaixo:

**1. O que, nesta atividade, pertence ao CI e o que pertence ao CD?**
- **CI (Integração Contínua):** Toda a etapa aglomerada no job `validate`. O processo diário e frequente de fazer checkout do código, instalar dependências, verificar o linting (`flake8`), rodar testes automatizados (`pytest`) e verificar estaticamente o código (`mypy`) garante que o código novo se integra ao sistema de forma saudável e inquebrável. O empacotamento do resultado (o `summary.csv`) é o limite lógico do CI.
- **CD (Entrega/Deployment Contínuo):** Representado pelo job `deploy`. Começa após a validação terminar com sucesso, assumindo a responsabilidade de promover de maneira automatizada e segura o resultado para o ambiente fim (nesta simulação, através dos logs "*Promovendo artefato para produção*"). 

**2. Qual a diferença conceitual entre o script Python e a esteira que o valida?**
- O **script Python (`pipeline.py`)** é o núcleo de negócio. É o conjunto lógico das instruções da aplicação (ler os dados da empresa, validar colunas, tirar métricas). 
- A **esteira (Workflow de CI/CD)** é a infraestrutura de apoio (ou DevOps workflow). Ela não resolve um problema de negócio da empresa (como contar vendas), ela resolve o problema logístico do **software**. Ela avalia o script Python como forma de garantir que desenvolvedores não introduzam regressões ou código de baixa qualidade em produção.

**3. Se houvesse um ambiente de staging, em que ponto ele entraria no fluxo?**
- O ambiente de staging ou homologação entraria como um intermédio (Job de `Deploy to Staging`) posicionado **entre a aprovação do `validate` e o deploy de `main`**. O fluxo ideal funcionaria gerando o artefato testado (CI), implantando no Staging para testes manuais/QA ou testes End-to-End integrados sistemicamente. Se os critérios fossem satisfeitos em Staging, o mesmo artefato promovido pelo Runner seria finalmente jogado para o ambiente produtivo.

---

## ▶️ Como reproduzir localmente

Se quiser validar o projeto na sua máquina:

```bash
# 1. Crie o ambiente virtual e ative-o:
python3 -m venv .pyenv
source .pyenv/bin/activate  # ou .pyenv\Scripts\Activate.ps1 no Windows

# 2. Instale as dependências:
pip install -r requirements.txt

# 3. Rode os testes e as análises estáticas:
flake8 app tests
mypy app
pytest

# 4. Gere o relatório do script principal:
python app/pipeline.py
```
*Após a execução, um arquivo `/output/summary.csv` será criado (mas ignorado pelo git para não poluir o repositório).*

<p align="center">
Desenvolvido com ☕ e 🤖 no laboratório de Engenharia para PUC Minas.
</p>
