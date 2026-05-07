# Estruturas de Controle

## If / Else

Sintaxe básica — sem parênteses obrigatórios:

```go
idade := 18

if idade >= 18 {
    fmt.Println("Maior de idade")
} else if idade >= 13 {
    fmt.Println("Adolescente")
} else {
    fmt.Println("Criança")
}
```

### If com inicialização

Go permite declarar uma variável diretamente no `if`:

```go
if err := fazerAlgo(); err != nil {
    fmt.Println("Erro:", err)
}
```

!!! tip "Boas práticas"
    Prefira o `if` com inicialização para manter o escopo da variável de erro restrito ao bloco `if`.

---

## For

Go tem **apenas um** laço de repetição: o `for`. Mas ele cobre todos os casos.

### Loop clássico

```go
for i := 0; i < 5; i++ {
    fmt.Println(i)
}
```

### Loop tipo "while"

```go
n := 1
for n < 100 {
    n *= 2
}
```

### Loop infinito

```go
for {
    // executa indefinidamente
    // use break para sair
}
```

### Range

Itera sobre slices, arrays, maps e strings:

```go
frutas := []string{"maçã", "banana", "laranja"}

for i, fruta := range frutas {
    fmt.Printf("%d: %s\n", i, fruta)
}
```

!!! tip "Ignorando o índice"
    Use `_` para ignorar o índice: `for _, fruta := range frutas { ... }`

---

## Switch

```go
dia := "segunda"

switch dia {
case "segunda", "terça", "quarta", "quinta", "sexta":
    fmt.Println("Dia útil")
case "sábado", "domingo":
    fmt.Println("Fim de semana")
default:
    fmt.Println("Dia inválido")
}
```

### Switch sem condição (substitui if-else chain)

```go
hora := 14

switch {
case hora < 12:
    fmt.Println("Bom dia")
case hora < 18:
    fmt.Println("Boa tarde")
default:
    fmt.Println("Boa noite")
}
```

!!! warning "Diferença do C/Java"
    Em Go, o `switch` **não** faz fallthrough automático. Para isso, use a palavra-chave `fallthrough` explicitamente.
