# Tratamento de Erros

## A filosofia do Go

Go não usa exceções (`try/catch`). Em vez disso, erros são **valores** retornados explicitamente pelas funções. Isso força o desenvolvedor a tratar cada erro no ponto onde ocorre.

```go
resultado, err := fazerAlgo()
if err != nil {
    // tratar o erro
}
```

---

## O tipo `error`

`error` é uma interface nativa do Go:

```go
type error interface {
    Error() string
}
```

### Criando erros simples

```go
import "errors"

func dividir(a, b float64) (float64, error) {
    if b == 0 {
        return 0, errors.New("divisão por zero não permitida")
    }
    return a / b, nil
}

resultado, err := dividir(10, 0)
if err != nil {
    fmt.Println("Erro:", err)
} else {
    fmt.Println("Resultado:", resultado)
}
```

### Com `fmt.Errorf` para mensagens formatadas

```go
import "fmt"

func buscarUsuario(id int) (*Usuario, error) {
    if id <= 0 {
        return nil, fmt.Errorf("ID inválido: %d", id)
    }
    // ...
}
```

---

## Erros customizados

Criando um tipo de erro com campos adicionais:

```go
type ErroPagamento struct {
    Codigo    int
    Mensagem  string
}

func (e *ErroPagamento) Error() string {
    return fmt.Sprintf("erro %d: %s", e.Codigo, e.Mensagem)
}

func processarPagamento(valor float64) error {
    if valor <= 0 {
        return &ErroPagamento{Codigo: 400, Mensagem: "valor inválido"}
    }
    return nil
}
```

---

## Unwrapping e `errors.Is` / `errors.As`

### `errors.Is` — verifica identidade do erro

```go
var ErrNaoEncontrado = errors.New("não encontrado")

func buscar(id int) error {
    return fmt.Errorf("buscar: %w", ErrNaoEncontrado) // %w faz o wrap
}

err := buscar(42)
if errors.Is(err, ErrNaoEncontrado) {
    fmt.Println("O item não foi encontrado")
}
```

### `errors.As` — extrai o tipo concreto do erro

```go
err := processarPagamento(-10)

var errPag *ErroPagamento
if errors.As(err, &errPag) {
    fmt.Printf("Código do erro: %d\n", errPag.Codigo)
}
```

!!! tip "Use `%w` ao envolver erros"
    Use `fmt.Errorf("contexto: %w", err)` para adicionar contexto ao erro sem perder a capacidade de inspecioná-lo com `errors.Is` e `errors.As`.

---

## `defer`, `panic` e `recover`

### defer

Adia a execução de uma função até o retorno da função atual — muito usado para fechar recursos:

```go
func lerArquivo(nome string) error {
    f, err := os.Open(nome)
    if err != nil {
        return err
    }
    defer f.Close() // garante fechamento mesmo se houver erro depois

    // ... ler o arquivo
    return nil
}
```

### panic e recover

Use `panic` apenas para erros **irrecuperáveis** (bugs de programação):

```go
func dividirForce(a, b int) int {
    if b == 0 {
        panic("divisão por zero: bug no código chamador")
    }
    return a / b
}
```

`recover` captura um `panic` — geralmente usado em servidores HTTP para evitar que um panic derrube o processo inteiro:

```go
func safeHandler() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Recuperado do panic:", r)
        }
    }()
    panic("algo deu muito errado")
}
```

!!! warning "panic não é exceção"
    Não use `panic` como substituto de tratamento de erros normal. Reserve-o para situações que indicam bugs no código, não falhas esperadas em runtime.
