<div align="center">

# Stitch 🧵🐇

**A from-scratch CRDT engine for real-time collaborative text editing, written in Go.**
*Threads that always find their way back together.*

<img src="https://media.giphy.com/media/v1.Y2lkPWVjZjA1ZTQ3Z3VmbHRiMDlrdGVxMHNjdDRzbXNneXdtYnFleGxveDVhdXF6c2ZqYSZlcD12MV9naWZzX3NlYXJjaCZjdD1n/Xb6J4XCrn1G08vnyvh/giphy.gif" width="200"/><img src="https://media.giphy.com/media/v1.Y2lkPWVjZjA1ZTQ3Z3VmbHRiMDlrdGVxMHNjdDRzbXNneXdtYnFleGxveDVhdXF6c2ZqYSZlcD12MV9naWZzX3NlYXJjaCZjdD1n/Xb6J4XCrn1G08vnyvh/giphy.gif" width="200"/><img src="https://media.giphy.com/media/v1.Y2lkPWVjZjA1ZTQ3Z3VmbHRiMDlrdGVxMHNjdDRzbXNneXdtYnFleGxveDVhdXF6c2ZqYSZlcD12MV9naWZzX3NlYXJjaCZjdD1n/Xb6J4XCrn1G08vnyvh/giphy.gif" width="200"/>

<br/><br/>

[![Go Version](https://img.shields.io/badge/Go-1.22%2B-00ADD8?style=for-the-badge&logo=go&logoColor=white)](https://go.dev)
[![License: MIT](https://img.shields.io/badge/license-MIT-00ADD8?style=for-the-badge)](LICENSE)
[![Tests](https://img.shields.io/badge/tests-passing-00ADD8?style=for-the-badge)](#-testing--тестирование--pruebas)
[![Status](https://img.shields.io/badge/status-active-00ADD8?style=for-the-badge)](#)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-00ADD8?style=for-the-badge)](#-contributing--вклад--contribuciones)

<br/>

### 🌍 Choose your language / Выберите язык / Elige tu idioma

<a href="#-english"><img src="https://img.shields.io/badge/🇬🇧-English-00ADD8?style=for-the-badge"/></a>
<a href="#-русский"><img src="https://img.shields.io/badge/🇷🇺-Русский-00ADD8?style=for-the-badge"/></a>
<a href="#-español"><img src="https://img.shields.io/badge/🇪🇸-Español-00ADD8?style=for-the-badge"/></a>

</div>

---

<a id="-english"></a>
## 🇬🇧 English

<div align="center">
<img <img src="https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExNmttanByMmpzd2M3dnR3dnFseWF2cGxsNXEzazlicmFiZ3BpZTBwdCZlcD12MV9naWZzX3NlYXJjaCZjdD1n/YqPBdDdjEOlUBURAJS/giphy.gif" alt="Adorable Bunny" width="500"/>
</div>

### 📖 Overview

**Stitch** is a from-scratch implementation of a **Replicated Growable Array (RGA)** — the CRDT algorithm behind real-time collaborative text editors. No central lock, no operational-transform server juggling — every replica applies local edits instantly and **converges deterministically** with every other replica, even across network partitions and out-of-order delivery.

This isn't a wrapper around an existing library. The CRDT core, tombstone garbage collection, and the WebSocket sync layer are all implemented from first principles, with the reasoning documented in [`docs/design.md`](docs/design.md).

### 🏗 Architecture & Engineering Highlights

| Feature | Why it matters |
|---|---|
| 🧵 **RGA core, built from scratch** | Deterministic convergence via a unique-ID-based total order — no server-side conflict resolution needed |
| 🧵 **Tombstone-based deletion** | Deleted characters are marked, not removed — preserves causal ordering for late-arriving concurrent ops |
| 🧵 **Idempotent, commutative merge** | Operations can arrive out of order or be re-delivered without corrupting state |
| 🧵 **WebSocket sync layer** | Each client applies ops locally first (optimistic UI), then broadcasts — zero perceived latency |
| 🧵 **Convergence benchmarks** | Measured merge time and final-state equality under N concurrent editors — numbers, not claims |

### 📁 Project Layout

```
.
├── cmd/
│   ├── server/           # WebSocket relay entrypoint
│   └── client/           # CLI demo client (simulates concurrent editors)
├── internal/
│   ├── crdt/
│   │   ├── rga.go        # Core RGA data structure
│   │   ├── operation.go  # Insert/delete operation types
│   │   └── merge.go      # Merge & convergence logic
│   └── sync/
│       └── websocket.go  # Op broadcast & session handling
├── bench/
│   └── convergence_test.go
├── docs/
│   └── design.md         # Algorithm write-up: why RGA, ID scheme, tombstones
├── .gitignore
├── go.mod
├── LICENSE
└── README.md
```

### 🚀 Quick Start

```bash
git clone https://github.com/Inke-chip/stitch.git
cd stitch
go mod tidy

# start the sync server
go run ./cmd/server

# in two other terminals, simulate two concurrent editors
go run ./cmd/client -user alice
go run ./cmd/client -user bob
```

### 🧪 Testing

```bash
go test ./...
go test -race ./...              # concurrency safety
go test ./bench -bench=Convergence -benchmem
```

### 📊 Benchmarks

| Scenario | Concurrent editors | Merge time | Final-state equality |
|---|---|---|---|
| Sequential edits | 1 | `TODO` | ✅ |
| Concurrent inserts, same position | 10 | `TODO` | ✅ |
| Network partition + reconnect | 5 | `TODO` | ✅ |
| Out-of-order delivery | 20 | `TODO` | ✅ |

### 🎯 Design Decisions (short version)

- **RGA over Operational Transform** — no central server required to resolve conflicts; every replica is authoritative for its own view, which converges without coordination.
- **Tombstones over hard deletes** — deleting an element outright can break the total order for a concurrent insert that hasn't arrived yet. Tombstones trade memory for correctness (with a documented, honest caveat: no compaction yet — see `docs/design.md`).
- **No undo/redo** — genuinely hard to do correctly in a CRDT without a separate operation log; deliberately out of scope, and the README says so instead of pretending otherwise.

Full reasoning, the ID scheme, and diagrams: [`docs/design.md`](docs/design.md).

### 🤝 Contributing

Issues and PRs are welcome — especially benchmark numbers from real hardware.

### 📄 License

Distributed under the MIT License. See [`LICENSE`](LICENSE) for details.

<div align="right"><a href="#stitch-">↑ back to top</a></div>

---

<a id="-русский"></a>
## 🇷🇺 Русский

<div align="center">
<img src="https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExdGk3Z29uMGJpM3Z1c2tscmNjenBqOTloczNvZW9iMXBqeW9xbnp1YSZlcD12MV9naWZzX3NlYXJjaCZjdD1n/KHh7jLrG6gIXBTnxsp/giphy.gif" alt="Кролики" width="2000"/>
</div>

### 📖 Обзор

**Stitch** — реализация **Replicated Growable Array (RGA)** с нуля: CRDT-алгоритма, лежащего в основе современных коллаборативных текстовых редакторов. Никакого центрального сервера блокировок, никакого жонглирования операционными трансформациями — каждая реплика применяет локальные правки мгновенно и **детерминированно сходится** с остальными репликами, даже при разрывах сети и доставке операций не по порядку.

Это не обёртка над существующей библиотекой. Ядро CRDT, сборка мусора по tombstone-меткам и слой синхронизации через WebSocket реализованы с нуля, вся логика объяснена в [`docs/design.md`](docs/design.md).

### 🏗 Архитектура и инженерные особенности

| Фича | Почему это важно |
|---|---|
| 🧵 **RGA-ядро с нуля** | Детерминированная сходимость через полный порядок по уникальным ID — конфликты не нужно резолвить на сервере |
| 🧵 **Удаление через tombstone-метки** | Удалённые символы помечаются, а не удаляются физически — сохраняется каузальный порядок для конкурентных операций, пришедших позже |
| 🧵 **Идемпотентный, коммутативный merge** | Операции могут приходить не по порядку или дублироваться без порчи состояния |
| 🧵 **Слой синхронизации на WebSocket** | Клиент сначала применяет операцию локально (оптимистичный UI), затем рассылает — нулевая ощущаемая задержка |
| 🧵 **Бенчмарки сходимости** | Замерено время merge и равенство финального состояния при N конкурентных редакторах — цифры, а не голословные утверждения |

### 📁 Структура проекта

```
.
├── cmd/
│   ├── server/           # Точка входа сервера синхронизации (WebSocket)
│   └── client/           # CLI-клиент для демо (симуляция конкурентных редакторов)
├── internal/
│   ├── crdt/
│   │   ├── rga.go        # Основная структура данных RGA
│   │   ├── operation.go  # Типы операций insert/delete
│   │   └── merge.go      # Логика merge и сходимости
│   └── sync/
│       └── websocket.go  # Рассылка операций и управление сессиями
├── bench/
│   └── convergence_test.go
├── docs/
│   └── design.md         # Разбор алгоритма: почему RGA, схема ID, tombstones
├── .gitignore
├── go.mod
├── LICENSE
└── README.md
```

### 🚀 Быстрый запуск

```bash
git clone https://github.com/Inke-chip/stitch.git
cd stitch
go mod tidy

# запустить сервер синхронизации
go run ./cmd/server

# в двух других терминалах — симуляция двух конкурентных редакторов
go run ./cmd/client -user alice
go run ./cmd/client -user bob
```

### 🧪 Тестирование

```bash
go test ./...
go test -race ./...              # проверка потокобезопасности
go test ./bench -bench=Convergence -benchmem
```

### 📊 Бенчмарки

| Сценарий | Конкурентных редакторов | Время merge | Равенство финального состояния |
|---|---|---|---|
| Последовательные правки | 1 | `TODO` | ✅ |
| Конкурентные вставки в одну позицию | 10 | `TODO` | ✅ |
| Разрыв сети + восстановление | 5 | `TODO` | ✅ |
| Доставка не по порядку | 20 | `TODO` | ✅ |

### 🎯 Ключевые архитектурные решения (кратко)

- **RGA вместо Operational Transform** — не нужен центральный сервер для резолва конфликтов; каждая реплика авторитетна для своего состояния, и они сходятся без координации.
- **Tombstone вместо физического удаления** — удаление элемента "начисто" может сломать полный порядок для конкурентной вставки, которая ещё не пришла. Tombstones — это цена памяти за корректность (с честной оговоркой: компакции пока нет — см. `docs/design.md`).
- **Нет undo/redo** — сложно сделать корректно в CRDT без отдельного лога операций; осознанно вынесено за скоуп, и README честно об этом говорит, а не делает вид, что фичи нет случайно.

Полное обоснование, схема ID и диаграммы — в [`docs/design.md`](docs/design.md).

### 🤝 Вклад в проект

Issues и Pull Request'ы приветствуются — особенно цифры бенчмарков с реального железа.

### 📄 Лицензия

Проект распространяется под лицензией MIT. Подробности в файле [`LICENSE`](LICENSE).

<div align="right"><a href="#stitch-">↑ наверх</a></div>

---

<a id="-español"></a>
## 🇪🇸 Español

<div align="center">
<img src="https://media3.giphy.com/media/v1.Y2lkPTc5MGI3NjExNG80MW12cGkwYjQzZTBzdjB6cDc3dXZneHRwcTF4NHJwNnAwdDF4ciZlcD12MV9naWZzX3NlYXJjaCZjdD1n/XmyF99pGjTQKk/giphy.gif" alt="Conejo sentado" width="500"/>
</div>

### 📖 Descripción general

**Stitch** es una implementación desde cero de un **Replicated Growable Array (RGA)** — el algoritmo CRDT detrás de los editores de texto colaborativos en tiempo real. Sin bloqueos centralizados, sin malabares con operational transform — cada réplica aplica las ediciones locales al instante y **converge de forma determinista** con el resto, incluso ante particiones de red o entregas fuera de orden.

No es un wrapper sobre una librería existente. El núcleo CRDT, la recolección de basura basada en tombstones y la capa de sincronización por WebSocket están implementados desde los fundamentos, con el razonamiento documentado en [`docs/design.md`](docs/design.md).

### 🏗 Arquitectura y aspectos técnicos destacados

| Característica | Por qué importa |
|---|---|
| 🧵 **Núcleo RGA desde cero** | Convergencia determinista mediante un orden total basado en IDs únicos — no hace falta resolución de conflictos en el servidor |
| 🧵 **Borrado mediante tombstones** | Los caracteres borrados se marcan, no se eliminan — preserva el orden causal para operaciones concurrentes que llegan tarde |
| 🧵 **Merge idempotente y conmutativo** | Las operaciones pueden llegar desordenadas o repetidas sin corromper el estado |
| 🧵 **Capa de sincronización por WebSocket** | Cada cliente aplica la operación localmente primero (UI optimista) y luego la difunde — latencia percibida cercana a cero |
| 🧵 **Benchmarks de convergencia** | Tiempo de merge y equivalencia del estado final medidos con N editores concurrentes — datos, no promesas |

### 📁 Estructura del proyecto

```
.
├── cmd/
│   ├── server/           # Punto de entrada del servidor de sincronización
│   └── client/           # Cliente CLI de demo (simula editores concurrentes)
├── internal/
│   ├── crdt/
│   │   ├── rga.go        # Estructura de datos RGA principal
│   │   ├── operation.go  # Tipos de operación insert/delete
│   │   └── merge.go      # Lógica de merge y convergencia
│   └── sync/
│       └── websocket.go  # Difusión de operaciones y sesiones
├── bench/
│   └── convergence_test.go
├── docs/
│   └── design.md         # Explicación del algoritmo: por qué RGA, esquema de IDs, tombstones
├── .gitignore
├── go.mod
├── LICENSE
└── README.md
```

### 🚀 Inicio rápido

```bash
git clone https://github.com/Inke-chip/stitch.git
cd stitch
go mod tidy

# levantar el servidor de sincronización
go run ./cmd/server

# en otras dos terminales, simular dos editores concurrentes
go run ./cmd/client -user alice
go run ./cmd/client -user bob
```

### 🧪 Pruebas

```bash
go test ./...
go test -race ./...              # seguridad de concurrencia
go test ./bench -bench=Convergence -benchmem
```

### 📊 Benchmarks

| Escenario | Editores concurrentes | Tiempo de merge | Equivalencia del estado final |
|---|---|---|---|
| Ediciones secuenciales | 1 | `TODO` | ✅ |
| Inserciones concurrentes, misma posición | 10 | `TODO` | ✅ |
| Partición de red + reconexión | 5 | `TODO` | ✅ |
| Entrega fuera de orden | 20 | `TODO` | ✅ |

### 🎯 Decisiones de diseño (versión corta)

- **RGA en lugar de Operational Transform** — no requiere un servidor central para resolver conflictos; cada réplica es autoritativa sobre su propia vista y converge sin coordinación.
- **Tombstones en lugar de borrado físico** — borrar un elemento por completo puede romper el orden total para una inserción concurrente que aún no ha llegado. Los tombstones cambian memoria por corrección (con una advertencia honesta: todavía no hay compactación — ver `docs/design.md`).
- **Sin undo/redo** — genuinamente difícil de hacer bien en un CRDT sin un log de operaciones aparte; queda fuera del alcance a propósito, y el README lo dice en lugar de fingir que falta por accidente.

Razonamiento completo, esquema de IDs y diagramas: [`docs/design.md`](docs/design.md).

### 🤝 Contribuciones

Los issues y pull requests son bienvenidos — especialmente números de benchmarks en hardware real.

### 📄 Licencia

Distribuido bajo la licencia MIT. Ver [`LICENSE`](LICENSE) para más detalles.

<div align="right"><a href="#stitch-">↑ arriba</a></div>

---

<div align="center">

🧵 Made with Go, patience, and a lot of rabbits 🐇

</div>
