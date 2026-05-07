# Sintaxe Básica e Variáveis

## Declaração de Variáveis

Go oferece duas formas principais de declarar variáveis:

### Declaração explícita

```go
var nome string = "Gabriel"
var idade int = 21
var ativo bool = true
```

### Declaração curta (`:=`)

```go
nome := "Gabriel"
idade := 21
ativo := true
```

!!! tip "Quando usar `:=`"
    Use `:=` dentro de funções para declarações rápidas. Fora de funções (escopo de pacote), use `var`.

---

## Tipos Primitivos

| Tipo       | Exemplo           | Descrição                    |
|------------|-------------------|------------------------------|
| `int`      | `42`              | Inteiro (tamanho da plataforma) |
| `int64`    | `int64(100)`      | Inteiro 64 bits              |
| `float64`  | `3.14`            | Ponto flutuante              |
| `string`   | `"olá"`           | Texto UTF-8                  |
| `bool`     | `true` / `false`  | Booleano                     |
| `byte`     | `'A'`             | Alias para `uint8`           |
| `rune`     | `'ã'`             | Alias para `int32` (Unicode) |

---

## Constantes

```go
const Pi = 3.14159
const Linguagem = "Go"
```

Constantes agrupadas com `iota`:

```go
const (
    Domingo = iota // 0
    Segunda        // 1
    Terca          // 2
    Quarta         // 3
)
```

---

## Zero Values

Em Go, toda variável declarada sem valor inicial recebe um **zero value**:

```go
var x int      // 0
var s string   // ""
var b bool     // false
var p *int     // nil
```

!!! warning "Cuidado com nil"
    Ponteiros, slices, maps, channels e interfaces têm zero value `nil`. Acessar um ponteiro nil causa `panic` em runtime.

---

## Conversão de Tipos

Go **não** faz conversão implícita. Você deve ser explícito:

```go
var x int = 42
var y float64 = float64(x)
var z int = int(y)
```
