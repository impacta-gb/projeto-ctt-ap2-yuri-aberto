# Gerenciamento de Pacotes (Go Modules)

## O que são Go Modules?

Go Modules é o sistema oficial de gerenciamento de dependências do Go (desde Go 1.11). Ele rastreia as dependências do projeto com dois arquivos: `go.mod` e `go.sum`.

---

## Iniciando um módulo

No WSL ou Git Bash, dentro do diretório do projeto:

```bash
mkdir meu-projeto
cd meu-projeto
go mod init github.com/seu-usuario/meu-projeto
```

Isso cria o `go.mod`:

```
module github.com/seu-usuario/meu-projeto

go 1.22
```

---

## Adicionando dependências

```bash
go get github.com/gin-gonic/gin
```

O Go atualiza automaticamente `go.mod` e `go.sum`.

### go.mod após adicionar dependência

```
module github.com/seu-usuario/meu-projeto

go 1.22

require (
    github.com/gin-gonic/gin v1.9.1
)
```

---

## Comandos essenciais

| Comando                  | Descrição                                                  |
|--------------------------|------------------------------------------------------------|
| `go mod init <modulo>`   | Inicializa um novo módulo                                  |
| `go get <pacote>`        | Adiciona ou atualiza uma dependência                       |
| `go mod tidy`            | Remove dependências não usadas e adiciona as faltantes     |
| `go mod download`        | Baixa todas as dependências para o cache local             |
| `go mod verify`          | Verifica integridade das dependências com o `go.sum`       |
| `go list -m all`         | Lista todas as dependências do módulo                      |

!!! tip "Sempre rode `go mod tidy`"
    Após modificar imports no código, rode `go mod tidy` para manter o `go.mod` e o `go.sum` sincronizados com o que realmente é usado.

---

## Estrutura de pacotes internos

```
meu-projeto/
├── go.mod
├── go.sum
├── main.go
├── internal/
│   ├── auth/
│   │   └── auth.go       # pacote auth
│   └── database/
│       └── db.go         # pacote database
└── pkg/
    └── utils/
        └── helpers.go    # pacote utils (exportável)
```

### Definindo e importando pacotes internos

```go
// internal/auth/auth.go
package auth

func Validar(token string) bool {
    return token != ""
}
```

```go
// main.go
package main

import (
    "fmt"
    "github.com/seu-usuario/meu-projeto/internal/auth"
)

func main() {
    ok := auth.Validar("meu-token")
    fmt.Println(ok)
}
```

!!! warning "Visibilidade por capitalização"
    Em Go, **identificadores com letra maiúscula são exportados** (públicos) e com letra minúscula são privados ao pacote.

    ```go
    func Publica() {}  // acessível fora do pacote
    func privada() {}  // acessível apenas dentro do pacote
    ```

---

## Versões e compatibilidade

```bash
# Instalar versão específica
go get github.com/gin-gonic/gin@v1.8.0

# Atualizar para a última versão
go get github.com/gin-gonic/gin@latest

# Remover dependência
go get github.com/gin-gonic/gin@none
go mod tidy
```

!!! info "go.sum e segurança"
    O arquivo `go.sum` armazena hashes criptográficos de cada dependência. Ele **deve ser commitado** no repositório para garantir builds reproduzíveis e detectar adulterações.
