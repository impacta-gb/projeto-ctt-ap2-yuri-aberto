# Concorrência II: Channels

## O que são Channels?

Channels são o mecanismo de **comunicação segura** entre goroutines. Pense neles como tubos: uma goroutine envia um valor, outra recebe.

```go
ch := make(chan int) // cria um channel de inteiros
```

---

## Enviando e recebendo

```go
package main

import "fmt"

func soma(a, b int, ch chan int) {
    ch <- a + b // envia o resultado para o channel
}

func main() {
    ch := make(chan int)

    go soma(3, 7, ch)

    resultado := <-ch // recebe o valor (bloqueia até chegar)
    fmt.Println("Soma:", resultado) // 10
}
```

---

## Channels Buffered

Channels sem buffer bloqueiam até que haja um receptor. Channels com buffer aceitam valores até o limite sem bloquear:

```go
// Sem buffer — síncrono
ch1 := make(chan string)

// Com buffer de 3 — aceita até 3 valores sem bloquear
ch2 := make(chan string, 3)
ch2 <- "a"
ch2 <- "b"
ch2 <- "c"
// ch2 <- "d" // bloquearia aqui — buffer cheio
```

!!! tip "Quando usar buffer"
    Use channels com buffer quando o produtor é mais rápido que o consumidor e você quer evitar bloqueio imediato, ou quando o número de mensagens é conhecido.

---

## Fechando e iterando channels

```go
func gerarNumeros(ch chan int) {
    for i := 0; i < 5; i++ {
        ch <- i
    }
    close(ch) // sinaliza que não haverá mais envios
}

func main() {
    ch := make(chan int)
    go gerarNumeros(ch)

    for n := range ch { // itera até o channel ser fechado
        fmt.Println(n)
    }
}
```

!!! warning "Quem fecha o channel?"
    Apenas o **produtor** (quem envia) deve fechar o channel. Enviar para um channel fechado causa `panic`.

---

## Select

`select` permite esperar em múltiplos channels simultaneamente — como um `switch` para channels:

```go
func main() {
    ch1 := make(chan string)
    ch2 := make(chan string)

    go func() { ch1 <- "um" }()
    go func() { ch2 <- "dois" }()

    select {
    case msg := <-ch1:
        fmt.Println("Recebido de ch1:", msg)
    case msg := <-ch2:
        fmt.Println("Recebido de ch2:", msg)
    }
}
```

### Select com timeout

```go
import "time"

select {
case resultado := <-ch:
    fmt.Println("Resultado:", resultado)
case <-time.After(2 * time.Second):
    fmt.Println("Timeout! Operação demorou demais.")
}
```

---

## Pipeline Pattern

Channels podem ser encadeados para criar pipelines de processamento:

```go
func gerar(nums ...int) <-chan int {
    out := make(chan int)
    go func() {
        for _, n := range nums {
            out <- n
        }
        close(out)
    }()
    return out
}

func quadrado(in <-chan int) <-chan int {
    out := make(chan int)
    go func() {
        for n := range in {
            out <- n * n
        }
        close(out)
    }()
    return out
}

func main() {
    // Pipeline: gerar → quadrado → imprimir
    for n := range quadrado(gerar(2, 3, 4, 5)) {
        fmt.Println(n) // 4, 9, 16, 25
    }
}
```

!!! info "Fan-out / Fan-in"
    Patterns avançados de concorrência como **fan-out** (distribuir trabalho entre múltiplas goroutines) e **fan-in** (agregar resultados) são construídos sobre channels. Consulte o blog oficial do Go para exemplos aprofundados.
