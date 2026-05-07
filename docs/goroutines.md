# Concorrência I: Goroutines

## O que são Goroutines?

Goroutines são funções executadas **concorrentemente** pelo runtime do Go. São muito mais leves que threads do sistema operacional — você pode ter milhares rodando simultaneamente com baixo consumo de memória.

```go
go minhaFuncao() // inicia uma goroutine
```

---

## Exemplo básico

```go
package main

import (
    "fmt"
    "time"
)

func saudar(nome string) {
    fmt.Printf("Olá, %s!\n", nome)
}

func main() {
    go saudar("Maria")
    go saudar("João")
    go saudar("Ana")

    time.Sleep(100 * time.Millisecond) // aguarda as goroutines terminarem
    fmt.Println("Fim do programa")
}
```

!!! warning "Cuidado com `time.Sleep`"
    Usar `time.Sleep` para sincronizar goroutines é uma má prática. Use `sync.WaitGroup` ou **channels** (próximo capítulo) para sincronização correta.

---

## sync.WaitGroup

`WaitGroup` é a forma correta de esperar um grupo de goroutines concluir:

```go
package main

import (
    "fmt"
    "sync"
)

func tarefa(id int, wg *sync.WaitGroup) {
    defer wg.Done() // sinaliza conclusão ao terminar
    fmt.Printf("Tarefa %d executando\n", id)
}

func main() {
    var wg sync.WaitGroup

    for i := 1; i <= 5; i++ {
        wg.Add(1) // incrementa o contador antes de iniciar
        go tarefa(i, &wg)
    }

    wg.Wait() // bloqueia até todas as goroutines chamarem Done()
    fmt.Println("Todas as tarefas concluídas")
}
```

---

## Race Conditions

Goroutines compartilhando dados sem sincronização causam **race conditions**:

```go
// CÓDIGO COM RACE CONDITION — NÃO FAÇA ISSO
var contador int

func incrementar() {
    contador++ // leitura + escrita não são atômicas!
}
```

### Solução 1: sync.Mutex

```go
import "sync"

var (
    contador int
    mu       sync.Mutex
)

func incrementar() {
    mu.Lock()
    contador++
    mu.Unlock()
}
```

### Solução 2: sync/atomic

```go
import "sync/atomic"

var contador int64

func incrementar() {
    atomic.AddInt64(&contador, 1)
}
```

!!! danger "Detectando race conditions"
    Execute seu programa com a flag `-race` para detectar race conditions automaticamente:

    ```bash
    go run -race main.go
    go test -race ./...
    ```

---

## Goroutines vs Threads

| Característica     | Thread OS         | Goroutine Go         |
|--------------------|-------------------|----------------------|
| Memória inicial    | ~1–8 MB           | ~2–8 KB              |
| Gerenciamento      | Sistema operacional | Runtime do Go      |
| Criação            | Custosa            | Barata               |
| Quantidade típica  | Centenas           | Milhares a milhões   |
| Comunicação        | Memória compartilhada | Channels (preferido) |

!!! tip "Filosofia Go"
    *"Do not communicate by sharing memory; instead, share memory by communicating."*
    — Provérbio oficial da linguagem Go. Use **channels** para comunicação entre goroutines.
