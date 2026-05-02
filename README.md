# golang-performance-benchmarch

🚀 Go Performance API

API de alta performance desenvolvida em Go para simular processamento de pedidos com foco em:

- Concorrência eficiente
- Baixa alocação de memória
- Cache em memória
- Benchmarking e profiling

---

📌 Objetivo

Demonstrar técnicas reais de otimização em Go usadas em sistemas de alta escala, incluindo:

- Worker Pool
- Cache com controle de concorrência
- Redução de alocações
- Benchmark com "testing"
- Profiling com "pprof"

---

🧱 Arquitetura

Client → HTTP Handler → Worker Pool → Service → Cache

---

⚙️ Tecnologias

- Go (Golang)
- net/http
- sync (Mutex / RWMutex)
- testing (benchmark)
- pprof (profiling)

---

🚀 Como rodar

git clone https://github.com/seu-usuario/go-performance-api
cd go-performance-api

go mod tidy
go run cmd/server/main.go

Servidor disponível em:

http://localhost:8080/order?id=1&value=100

---

📊 Benchmark

Rodar benchmark:

go test -bench=. -benchmem

Exemplo de saída:

BenchmarkProcess-8    200000    8000 ns/op    0 B/op    0 allocs/op

---

🔬 Profiling

CPU

go test -bench=. -cpuprofile=cpu.out
go tool pprof cpu.out

Memória

go test -bench=. -memprofile=mem.out

---

⚡ Técnicas de Performance Utilizadas

1. Worker Pool

Evita criação excessiva de goroutines e controla concorrência.

2. Cache em memória

Reduz processamento repetido.

3. Minimização de alocações

Uso de estruturas eficientes e reaproveitamento de dados.

4. Paralelismo controlado

Uso de "GOMAXPROCS" para melhor uso da CPU.

---

📈 Possíveis Melhorias

- Substituir "encoding/json" por biblioteca mais rápida
- Adicionar Redis como cache externo
- Implementar rate limiting
- Adicionar métricas com Prometheus
- Teste de carga com k6

---

🧠 Aprendizados Demonstrados

- Escrita de código performático em Go
- Diagnóstico com profiling
- Design concorrente eficiente
- Boas práticas de backend

---

📚 Referências

- https://go.dev/doc/
- https://go.dev/blog/pprof
- https://pkg.go.dev/testing

---

👨‍💻 Autor

Fábio — Desenvolvedor de Software

---

📄 Licença

MIT
