# Testes Automatizados em Go

## Introdução

Go possui suporte nativo a testes — sem frameworks externos necessários. O pacote `testing` da stdlib é tudo que você precisa para começar.

---

## Estrutura básica de um teste

Para testar `math.go`, crie `math_test.go` no mesmo pacote:

```go
// math.go
package calc

func Soma(a, b int) int {
    return a + b
}

func Dividir(a, b float64) (float64, error) {
    if b == 0 {
        return 0, fmt.Errorf("divisão por zero")
    }
    return a / b, nil
}
```

```go
// math_test.go
package calc

import "testing"

func TestSoma(t *testing.T) {
    resultado := Soma(2, 3)
    esperado := 5

    if resultado != esperado {
        t.Errorf("Soma(2, 3) = %d; esperado %d", resultado, esperado)
    }
}
```

---

## Executando os testes

No WSL ou Git Bash:

```bash
# Roda todos os testes do pacote atual
go test ./...

# Com detalhes (-v)
go test -v ./...

# Rodar apenas um teste específico
go test -run TestSoma ./...

# Com cobertura de código
go test -cover ./...

# Gerar relatório de cobertura em HTML
go test -coverprofile=coverage.out ./...
go tool cover -html=coverage.out
```

---

## Table-Driven Tests

O padrão mais usado em Go — testa múltiplos casos com uma estrutura de tabela:

```go
func TestSomaTabela(t *testing.T) {
    casos := []struct {
        nome     string
        a, b     int
        esperado int
    }{
        {"positivos", 2, 3, 5},
        {"negativos", -1, -4, -5},
        {"zero", 0, 0, 0},
        {"misto", -3, 7, 4},
    }

    for _, tc := range casos {
        t.Run(tc.nome, func(t *testing.T) {
            resultado := Soma(tc.a, tc.b)
            if resultado != tc.esperado {
                t.Errorf("Soma(%d, %d) = %d; esperado %d",
                    tc.a, tc.b, resultado, tc.esperado)
            }
        })
    }
}
```

!!! tip "t.Run para sub-testes"
    `t.Run` permite nomear cada caso de teste individualmente. Com `go test -v`, você verá cada caso listado separadamente na saída.

---

## Testando erros

```go
import "errors"

func TestDividirPorZero(t *testing.T) {
    _, err := Dividir(10, 0)

    if err == nil {
        t.Fatal("esperava um erro, mas não recebeu nenhum")
    }
}

func TestDividirValido(t *testing.T) {
    resultado, err := Dividir(10, 2)

    if err != nil {
        t.Fatalf("erro inesperado: %v", err)
    }
    if resultado != 5 {
        t.Errorf("esperava 5, obteve %v", resultado)
    }
}
```

---

## Benchmarks

```go
func BenchmarkSoma(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Soma(100, 200)
    }
}
```

Executar:

```bash
go test -bench=. ./...
go test -bench=BenchmarkSoma -benchmem ./...
```

---

## Métodos de falha

| Método           | Comportamento                                              |
|------------------|------------------------------------------------------------|
| `t.Error(...)`   | Registra falha, continua o teste                          |
| `t.Errorf(...)`  | Registra falha formatada, continua o teste                |
| `t.Fatal(...)`   | Registra falha e **para** o teste imediatamente           |
| `t.Fatalf(...)` | Registra falha formatada e **para** o teste               |
| `t.Skip(...)`    | Pula o teste (útil para condicionais de ambiente)         |

!!! warning "t.Fatal vs t.Error"
    Use `t.Fatal` quando continuar o teste após a falha não faz sentido (ex: se a criação de um recurso falhou, não faz sentido testá-lo). Use `t.Error` quando quer coletar múltiplas falhas em uma única execução.

---

## Testes com race detector

```bash
go test -race ./...
```

!!! danger "Execute sempre com `-race` no CI"
    O race detector detecta condições de corrida em testes concorrentes. Adicione essa flag no pipeline de CI/CD para garantir segurança na concorrência.
