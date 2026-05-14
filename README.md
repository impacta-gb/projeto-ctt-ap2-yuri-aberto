[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/Du716tKn)

# 📘 Go Lang Docs — CTT AP2

Documentação completa da linguagem Go, construída com **Zensical** e publicada via **GitHub Pages** com pipeline de CI/CD automatizado no **GitHub Actions**.

---

## 👥 Integrantes do Grupo

| Nome                   | Responsabilidade principal                          |
|------------------------|-----------------------------------------------------|
| Gabriel Vieira         | Introdução e Instalação, Sintaxe Básica e Variáveis |
| Pedro Henrique Brandão | Estruturas de Controle, Arrays, Slices e Maps       |
| Vinicius               | Structs, Métodos e Tratamento de Erros              |
| Arthur Cesar           | Concorrência I: Goroutines, Concorrência II: Channels |
| Felipe Nere            | Gerenciamento de Pacotes, Testes Automatizados e configuração do pipeline CI/CD |

---

## 🌐 Site publicado

🔗 [https://\<seu-usuario\>.github.io/\<nome-do-repo\>](https://seu-usuario.github.io/nome-do-repo)

---

## 🔀 Fluxo de Trabalho Colaborativo (Git Flow)

### Regras do repositório

- A branch `main` está **protegida**: commits diretos estão bloqueados via **Branch Ruleset** configurado nas Settings do repositório.
- Toda alteração exige a abertura de um **Pull Request**.
- O PR precisa da **aprovação de pelo menos 1 outro membro** antes do merge.
- Force pushes estão bloqueados e deleção direta da `main` é restrita.

### Convenção de branches adotada pelo grupo

```
feat/<topico>     → nova página ou funcionalidade
fix/<topico>      → correção em página ou workflow
chore/<topico>    → ajustes de configuração (zensical.toml, README etc.)
```

Branches criadas no projeto:

```
feat/workflow-cicd-v2         → pipeline CI/CD (Felipe)
feat/doc-introducao-sintaxe   → Introdução e Sintaxe (Gabriel)
feat/doc-controle-colecoes    → Controle e Coleções (Pedro)
feat/doc-structs-erros        → Structs e Erros (Vinicius)
feat/doc-goroutines-channels  → Goroutines e Channels (Arthur)
feat/doc-modulos-testes       → Módulos e Testes (Felipe)
chore/update-readme           → Atualização do README (Gabriel)
```

### Fluxo passo a passo adotado

```bash
# 1. Atualizar a main local
git checkout main
git pull origin main

# 2. Criar a branch da feature
git checkout -b feat/doc-goroutines-channels

# 3. Fazer as alterações e commitar
git add docs/goroutines.md docs/channels.md
git commit -m "feat: adiciona páginas de goroutines e channels"

# 4. Subir a branch
git push origin feat/doc-goroutines-channels

# 5. Abrir Pull Request no GitHub para a main
# 6. Outro membro revisa, comenta e aprova
# 7. Realizar o merge via interface do GitHub
```

### Divisão de revisões (Code Review)

| PR | Autor | Revisor |
|---|---|---|
| feat/workflow-cicd-v2 | Felipe | Gabriel |
| feat/doc-introducao-sintaxe | Gabriel | Pedro |
| feat/doc-controle-colecoes | Pedro | Vinicius |
| feat/doc-structs-erros | Vinicius | Arthur |
| feat/doc-goroutines-channels | Arthur | Felipe |
| feat/doc-modulos-testes | Felipe | Gabriel |
| chore/update-readme | Gabriel | Pedro |

### Critérios de revisão

- Clareza do conteúdo e exemplos de código funcionais
- Uso correto de admonitions (`!!! tip`, `!!! warning`, `!!! danger`)
- Tabelas bem formatadas e navegação lateral coerente
- Comentários construtivos feitos na aba **Files changed** do PR
- Após aprovação, o próprio autor realiza o merge

---

## ⚙️ Arquitetura do Pipeline CI/CD

O workflow está em `.github/workflows/ci.yml`.

### Gatilhos (`on`)

| Evento             | Quando ocorre                              |
|--------------------|--------------------------------------------|
| `pull_request`     | A cada PR aberto ou atualizado para `main` |
| `push`             | Após merge na `main`                       |
| `schedule (cron)`  | Todo domingo à meia-noite UTC (`0 0 * * 0`) |

### Jobs e dependências

```
validate (matrix: Python 3.10 + 3.11)
    └─▶ build_site
            └─▶ deploy_site  ← só executa em push/schedule (nunca em PR)
```

### Descrição de cada job

#### `validate`
- Usa **Matrix Strategy** com Python `3.10` e `3.11`.
- Garante que o site compila sem erros em ambos os ambientes.
- Implementa **cache pip** com `actions/cache`, usando hash do `requirements.txt` como chave.

#### `build_site`
- Executa apenas após `validate` passar (`needs: validate`).
- Faz o build final com Python 3.11.
- Faz upload do artefato gerado (`./site`) com `actions/upload-pages-artifact`.

#### `deploy_site`
- Executa apenas após `build_site` (`needs: build_site`).
- **Condicional de segurança**: `if: github.event_name == 'push' || github.event_name == 'schedule'`
- Nunca roda durante Pull Requests — apenas após merge ou via agendamento.
- Faz download do artefato e publica no **GitHub Pages** com `actions/deploy-pages`.

### Fluxo visual

```
PR aberto
  │
  ├─ validate (3.10) ──┐
  ├─ validate (3.11) ──┴── build_site ──── ✗ deploy (bloqueado em PR)
  │
push na main (após merge)
  │
  ├─ validate (3.10) ──┐
  ├─ validate (3.11) ──┴── build_site ──── ✔ deploy_site → GitHub Pages
```

---

## 🚀 Como rodar localmente (WSL / Git Bash)

```bash
# Instalar dependências
pip install -r requirements.txt

# Rodar servidor local com hot-reload
zensical serve

# Build estático
zensical build
```

O site gerado fica em `./site/`.

---

## 📁 Estrutura do repositório

```
.
├── .github/
│   └── workflows/
│       └── ci.yml          # Pipeline CI/CD
├── docs/
│   ├── index.md
│   ├── introducao.md
│   ├── sintaxe.md
│   ├── controle.md
│   ├── colecoes.md
│   ├── structs.md
│   ├── erros.md
│   ├── goroutines.md
│   ├── channels.md
│   ├── modulos.md
│   └── testes.md
├── requirements.txt
├── zensical.toml
└── README.md
```
