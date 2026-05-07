# Arrays, Slices e Maps

## Arrays

Arrays têm tamanho **fixo** e definido na declaração:

```go
var numeros [5]int
numeros[0] = 10

primes := [5]int{2, 3, 5, 7, 11}
fmt.Println(primes[2]) // 5
```

!!! warning "Arrays em Go"
    Arrays são valores, não referências. Ao passar um array para uma função, uma cópia é feita. Prefira **slices** na maioria dos casos.

---

## Slices

Slices são vistas dinâmicas sobre arrays — o tipo mais comum em Go:

```go
// Declaração
frutas := []string{"maçã", "banana", "laranja"}

// Acesso
fmt.Println(frutas[0]) // maçã

// Adicionar elemento
frutas = append(frutas, "uva")

// Fatiamento [início:fim]
fmt.Println(frutas[1:3]) // [banana laranja]
```

### Criando com make

```go
s := make([]int, 5)     // len=5, cap=5
s2 := make([]int, 3, 10) // len=3, cap=10
```

### Propriedades de um slice

```go
s := []int{1, 2, 3, 4, 5}
fmt.Println(len(s)) // 5  — comprimento atual
fmt.Println(cap(s)) // 5  — capacidade do array subjacente
```

!!! tip "append e realocação"
    Quando `append` excede a capacidade, Go aloca um novo array maior automaticamente. Isso é transparente mas pode impactar performance em loops críticos — pré-aloque com `make` quando souber o tamanho.

---

## Maps

Maps são coleções de pares chave-valor:

```go
// Declaração e inicialização
capitais := map[string]string{
    "Brasil":    "Brasília",
    "Argentina": "Buenos Aires",
    "França":    "Paris",
}

// Acesso
fmt.Println(capitais["Brasil"]) // Brasília

// Inserção / atualização
capitais["Japão"] = "Tóquio"

// Remoção
delete(capitais, "França")
```

### Verificando existência de chave

```go
valor, ok := capitais["Brasil"]
if ok {
    fmt.Println("Capital:", valor)
} else {
    fmt.Println("Chave não encontrada")
}
```

### Iterando

```go
for pais, capital := range capitais {
    fmt.Printf("%s → %s\n", pais, capital)
}
```

!!! warning "Maps não são ordenados"
    A ordem de iteração em maps é **aleatória** em Go. Nunca assuma uma ordem específica.

!!! danger "Map nil"
    Um map declarado mas não inicializado é `nil`. Tentar escrever em um map nil causa `panic`. Sempre inicialize com `make` ou literal `{}`.

```go
// ERRADO — causa panic
var m map[string]int
m["chave"] = 1

// CORRETO
m := make(map[string]int)
m["chave"] = 1
```
