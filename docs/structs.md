# Structs e Métodos

## Structs

Structs agrupam campos relacionados em um único tipo:

```go
type Pessoa struct {
    Nome  string
    Idade int
    Email string
}

// Criando uma instância
p := Pessoa{
    Nome:  "Gabriel",
    Idade: 21,
    Email: "gabriel@email.com",
}

fmt.Println(p.Nome) // Gabriel
```

### Structs aninhadas

```go
type Endereco struct {
    Rua    string
    Cidade string
}

type Usuario struct {
    Nome     string
    Endereco Endereco
}

u := Usuario{
    Nome: "Ana",
    Endereco: Endereco{
        Rua:    "Av. Paulista",
        Cidade: "São Paulo",
    },
}

fmt.Println(u.Endereco.Cidade) // São Paulo
```

---

## Métodos

Métodos são funções associadas a um tipo (receiver):

```go
type Retangulo struct {
    Largura float64
    Altura  float64
}

func (r Retangulo) Area() float64 {
    return r.Largura * r.Altura
}

func (r Retangulo) Perimetro() float64 {
    return 2 * (r.Largura + r.Altura)
}

rect := Retangulo{Largura: 10, Altura: 5}
fmt.Println(rect.Area())      // 50
fmt.Println(rect.Perimetro()) // 30
```

---

## Pointer Receivers

Use um **pointer receiver** quando o método precisa modificar o struct:

```go
type Contador struct {
    valor int
}

func (c *Contador) Incrementar() {
    c.valor++
}

func (c Contador) Valor() int {
    return c.valor
}

c := Contador{}
c.Incrementar()
c.Incrementar()
fmt.Println(c.Valor()) // 2
```

!!! tip "Value vs Pointer Receiver"
    Use **value receiver** (`r Retangulo`) quando o método não modifica o struct.
    Use **pointer receiver** (`c *Contador`) quando o método precisa modificar o struct ou quando o struct é grande (evita cópia).

---

## Interfaces

Interfaces definem comportamentos. Um tipo implementa uma interface automaticamente se possuir todos os métodos dela:

```go
type Forma interface {
    Area() float64
    Perimetro() float64
}

type Circulo struct {
    Raio float64
}

func (c Circulo) Area() float64 {
    return 3.14159 * c.Raio * c.Raio
}

func (c Circulo) Perimetro() float64 {
    return 2 * 3.14159 * c.Raio
}

// Retangulo já implementa Forma (métodos definidos acima)
// Circulo também implementa Forma

func ImprimirInfo(f Forma) {
    fmt.Printf("Área: %.2f | Perímetro: %.2f\n", f.Area(), f.Perimetro())
}

ImprimirInfo(Retangulo{Largura: 4, Altura: 3})
ImprimirInfo(Circulo{Raio: 5})
```

!!! info "Duck Typing implícito"
    Go não usa `implements` como Java. Se o tipo tem os métodos, ele satisfaz a interface automaticamente — isso é chamado de **structural typing**.

---

## Struct Embedding (Composição)

Go favorece **composição** no lugar de herança:

```go
type Animal struct {
    Nome string
}

func (a Animal) Falar() string {
    return "..."
}

type Cachorro struct {
    Animal        // embedding — herda campos e métodos
    Raca  string
}

func (c Cachorro) Falar() string {
    return "Au au!"
}

d := Cachorro{Animal: Animal{Nome: "Rex"}, Raca: "Labrador"}
fmt.Println(d.Nome)    // Rex (campo promovido)
fmt.Println(d.Falar()) // Au au!
```
