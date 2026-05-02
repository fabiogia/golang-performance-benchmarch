🚀 Projeto: API de Processamento de Pedidos (High Performance)

🎯 Objetivo

Simular uma API que:

- recebe pedidos
- processa com alta concorrência
- usa cache
- retorna JSON rápido
- mede performance com benchmark + profiling

🧱 Estrutura do Projeto

```
go-performance-api/
│
├── cmd/
│   └── server/
│       └── main.go
│
├── internal/
│   ├── handler/
│   │   └── order_handler.go
│   │
│   ├── service/
│   │   └── order_service.go
│   │
│   ├── worker/
│   │   └── pool.go
│   │
│   ├── cache/
│   │   └── cache.go
│   │
│   └── model/
│       └── order.go
│
├── benchmark/
│   └── order_benchmark_test.go
│
├── go.mod
└── README.md
```


🧩 1. Modelo

```Go
package model

type Order struct {
    ID    int
    Value float64
}
```

⚡ 2. Worker Pool (Alta Performance)

```Go
package worker

type Job func()

type Pool struct {
    jobs chan Job
}

func NewPool(workerCount int) *Pool {
    p := &Pool{
        jobs: make(chan Job, 1000),
    }

    for i := 0; i < workerCount; i++ {
        go func() {
            for job := range p.jobs {
                job()
            }
        }()
    }

    return p
}

func (p *Pool) Submit(job Job) {
    p.jobs <- job
}
```

🧠 3. Cache em Memória

```Go
package cache

import "sync"

type Cache struct {
    data map[int]float64
    mu   sync.RWMutex
}

func NewCache() *Cache {
    return &Cache{
        data: make(map[int]float64),
    }
}

func (c *Cache) Get(id int) (float64, bool) {
    c.mu.RLock()
    defer c.mu.RUnlock()
    val, ok := c.data[id]
    return val, ok
}

func (c *Cache) Set(id int, value float64) {
    c.mu.Lock()
    defer c.mu.Unlock()
    c.data[id] = value
}
```

⚙️ 4. Service (com simulação pesada)

```Go
package service

import (
    "math"
    "time"

    "go-performance-api/internal/cache"
)

type OrderService struct {
    cache *cache.Cache
}

func NewOrderService(c *cache.Cache) *OrderService {
    return &OrderService{cache: c}
}

func (s *OrderService) Process(id int, value float64) float64 {
    if val, ok := s.cache.Get(id); ok {
        return val
    }

    // Simulação de processamento pesado
    result := math.Sqrt(value)
    time.Sleep(10 * time.Millisecond)

    s.cache.Set(id, result)
    return result
}
```

🌐 5. Handler HTTP

```Go
package handler

import (
    "encoding/json"
    "net/http"
    "strconv"

    "go-performance-api/internal/service"
    "go-performance-api/internal/worker"
)

type OrderHandler struct {
    service *service.OrderService
    pool    *worker.Pool
}

func NewOrderHandler(s *service.OrderService, p *worker.Pool) *OrderHandler {
    return &OrderHandler{s, p}
}

func (h *OrderHandler) Handle(w http.ResponseWriter, r *http.Request) {
    id, _ := strconv.Atoi(r.URL.Query().Get("id"))
    value, _ := strconv.ParseFloat(r.URL.Query().Get("value"), 64)

    done := make(chan float64)

    h.pool.Submit(func() {
        result := h.service.Process(id, value)
        done <- result
    })

    result := <-done

    json.NewEncoder(w).Encode(map[string]float64{
        "result": result,
    })
}
```

🚀 6. Main

```Go
package main

import (
    "net/http"
    "runtime"

    "go-performance-api/internal/cache"
    "go-performance-api/internal/handler"
    "go-performance-api/internal/service"
    "go-performance-api/internal/worker"
)

func main() {
    runtime.GOMAXPROCS(runtime.NumCPU())

    cache := cache.NewCache()
    service := service.NewOrderService(cache)
    pool := worker.NewPool(100)

    handler := handler.NewOrderHandler(service, pool)

    http.HandleFunc("/order", handler.Handle)

    http.ListenAndServe(":8080", nil)
}
```

