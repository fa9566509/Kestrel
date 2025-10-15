# 🦅 Kestrel Programming Language

> **Zero-cost clarity** — All the control of C, none of the footguns.

Kestrel is a **low-level systems programming language** built for developers who want the power of C **without the undefined behavior**, the flexibility of Zig **without the magic**, and the compile-time performance of Rust **without the ceremony**.

Every decision in Kestrel follows one principle:

> **Everything you see is everything that happens.**

---

## ✨ Philosophy

Kestrel is designed to make **memory safety, predictability, and performance** the default, without hiding how the machine actually works.

* 🧠 **Explicit ownership** — no GC, no hidden moves.
* 🔥 **No undefined behavior** — every operation is well-defined.
* 🧉 **Comptime without chaos** — clear, explicit compile-time execution.
* 🥱 **Transparent layout** — structs, padding, and alignment are visible.
* 🥾 **Error handling done right** — no exceptions, no silent failures.
* ⚙️ **Zero runtime** — you own your memory, not the compiler.

---

## 🥬 Example

```kestrel
const BUFFER_SIZE = 4096

struct Connection {
    socket: i32
    buffer: [BUFFER_SIZE]u8
    pos: usize
} align(64)

fn create_connection(host: str, port: u16) -> *Connection | Error {
    let conn = allocate(Connection)
    defer on_error free(conn)

    conn^.socket = syscall_socket()?
    defer on_error syscall_close(conn^.socket)

    syscall_connect(conn^.socket, host, port)?
    conn^.pos = 0
    return conn
}

fn read_line(conn: *Connection) -> str | Error {
    while conn^.pos < BUFFER_SIZE {
        let bytes = syscall_read(conn^.socket, conn^.buffer[conn^.pos..], 1)?
        if bytes == 0 then return Error.Closed

        if conn^.buffer[conn^.pos] == '\n' {
            let line = conn^.buffer[0..conn^.pos]
            conn^.pos = 0
            return line
        }

        conn^.pos += bytes
    }
    return Error.BufferFull
}

fn close_connection(own conn: *Connection) {
    syscall_close(conn^.socket)
    free(conn)
}
```

---

## 🧭 Core Concepts

| Feature                  | Description                                                        |                                               |     |       |
| ------------------------ | ------------------------------------------------------------------ | --------------------------------------------- | --- | ----- |
| **Ownership**            | Explicit syntax for `own`, `ref`, and borrow semantics.            |                                               |     |       |
| **Pointers**             | `*T` (non-null) and `*?T` (nullable) — clear and safe.             |                                               |     |       |
| **Slices**               | `[]T` with runtime length; bounds-checked in debug mode.           |                                               |     |       |
| **Error Handling**       | Union types (`T                                                    | Error`) with explicit propagation (`?`or`else | err | {}`). |
| **Memory Layout**        | `packed` and `align(N)` control binary layout exactly.             |                                               |     |       |
| **Comptime**             | Deterministic compile-time code execution.                         |                                               |     |       |
| **No Implicit Anything** | No coercions, conversions, promotions, or constructors.            |                                               |     |       |
| **Defer**                | Scoped cleanup like Go, with `defer on_error` for error unwinding. |                                               |     |       |
| **Inline Assembly**      | Explicit, typed `asm` blocks with named inputs/outputs.            |                                               |     |       |

---

## 🥉 Language Goals

| Goal                 | Description                                             |
| -------------------- | ------------------------------------------------------- |
| **Safety**           | No use-after-free, no null deref, no UB by default.     |
| **Simplicity**       | Minimal keywords (~30), orthogonal features.            |
| **Speed**            | Zero-runtime, zero hidden allocations, zero exceptions. |
| **Interoperability** | Planned full C ABI compatibility.                       |
| **Composability**    | Designed to scale from kernel code to compilers.        |

---

## 🧠 Syntax Quick Reference

```kestrel
fn process(own data: []u8) -> i32 | Error {
    if data.len == 0 then return Error.Empty
    defer free(data)
    return cast(i32) data[0]
}
```

| Syntax         | Meaning                    |
| -------------- | -------------------------- |
| `own`          | Ownership transfer         |
| `ref`          | Mutable borrow             |
| `*T` / `*?T`   | Pointer / Nullable pointer |
| `[]T` / `[N]T` | Slice / Fixed array        |
| `x.copy()`     | Explicit copy              |
| `cast(T) x`    | Explicit cast              |
| `?`            | Propagate error            |
| `defer`        | Run at scope exit          |
| `comptime`     | Compile-time execution     |

---

## 🔬 Example: Compile-Time Code

```kestrel
const TABLE = comptime {
    let arr: [10]i32
    for i in 0..10 {
        arr[i] = i * i
    }
    arr
}
```

---

## 🧰 Building (Planned)

The Kestrel compiler is in active design.
In future versions:

```bash
kestrel build hello.kes
kestrel run tests/
kestrel fmt src/
```

> Targeting LLVM backend for first implementation.

---

## 📘 Documentation

* [Language Specification](docs/spec.md)
* [Grammar (EBNF)](docs/grammar.md) *(coming soon)*
* [Examples](examples/)
* [Design Notes](docs/design_philosophy.md)

---

## 🛠️ Contributing

Kestrel is currently in **design phase (v0.1)**.
If you want to contribute ideas, syntax proposals, or compiler front-end code:

1. Fork this repository
2. Create a branch (`feature/ownership-checker`)
3. Submit a PR with a clear design note

Guidelines:

* Clarity > Cleverness
* Explicit > Implicit
* Performance > Abstraction

---

## 🥉 Roadmap

| Milestone | Focus                                     |
| --------- | ----------------------------------------- |
| v0.1      | Language spec + parser prototype          |
| v0.2      | Type checker + ownership semantics        |
| v0.3      | LLVM backend + standard library bootstrap |
| v1.0      | Self-hosted compiler                      |

---

## 🧠 Motto

> If it compiles, it’s safe.
> If it’s unsafe, you wrote `unchecked`.

---

## 📄 License

MIT License © 2025 — Kestrel Language Authors
Open for community contributions.
