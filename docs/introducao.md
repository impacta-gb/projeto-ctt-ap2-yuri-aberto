# Introdução e Instalação

## O que é Go?

Go é uma linguagem compilada, estaticamente tipada, desenvolvida por engenheiros do Google (Robert Griesemer, Rob Pike e Ken Thompson). Seu foco é produtividade do desenvolvedor aliada à performance de sistemas.

!!! info "Curiosidade"
    Go foi criado em 2007 e lançado publicamente em 2009. Hoje é usado por empresas como Docker, Kubernetes, Uber e Cloudflare.

---

## Instalação

### Windows / macOS / Linux

1. Acesse [https://go.dev/dl/](https://go.dev/dl/)
2. Baixe o instalador para o seu sistema operacional
3. Siga o assistente de instalação

### Verificando a instalação

Após instalar, abra o terminal e execute:

```bash
go version
```

Saída esperada:

```
go version go1.22.0 linux/amd64
```

---

## Primeiro Programa

Crie um arquivo chamado `main.go`:

```go
package main

import "fmt"

func main() {
    fmt.Println("Olá, Go!")
}
```

Execute com:

```bash
go run main.go
```

Ou compile e execute:

```bash
go build -o meu_programa main.go
./meu_programa
```

!!! tip "go run vs go build"
    Use `go run` para desenvolvimento rápido. Use `go build` para gerar um binário para produção.

---

## Estrutura de um projeto Go

```
meu-projeto/
├── go.mod          # Definição do módulo e dependências
├── go.sum          # Checksums das dependências
├── main.go         # Ponto de entrada da aplicação
└── internal/       # Pacotes internos (não exportados)
```

!!! warning "Atenção"
    Todo programa Go executável deve ter uma função `main()` dentro do pacote `main`.
