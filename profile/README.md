**English** · [Русский](README.ru.md)

# Folk

**A fast, reliable PHP application server — built by the folk, written by AI.**

Folk runs your PHP application as a long-lived process instead of booting it from scratch on every request. It's a runtime written in **Rust** and embedded as a native **PHP extension**, so there's no separate server speaking some wire protocol to your code — PHP runs *in process*, next to the runtime.

The goal is simple: **performance and reliability without the tambourine dance** around installing and configuring an app server.

Folk needs **PHP built with ZTS** (Zend Thread Safety). Then it's two steps with standard tooling:

```bash
# 1. Install the Folk extension — a pre-built binary, no Rust toolchain needed
pie install folk-project/ext-folk

# 2. Add the adapter for your framework
composer require folk/laravel   # or folk/symfony, folk/spiral, folk/yii3
```

[PIE](https://github.com/php/pie) is the PHP Foundation's extension installer; Composer pulls in the adapter. No hand-rolled worker pools, no protocol glue.

---

## Who Folk is for

PHP developers who want serious throughput **without**:

- switching to Go or another language,
- hand-managing worker pools and event loops,
- fighting a fragile install-and-configure ritual every time.

If you have a Laravel / Symfony / Spiral / Yii3 app and you want it fast and stable, Folk is for you.

## Why Folk is different

Most "speed up PHP" tools run a separate server process that talks to your PHP workers over a socket or pipe with some wire protocol. That adds moving parts, serialization overhead, and a second thing to configure and keep alive.

Folk takes a different path:

- **In-process, via a PHP extension.** The Rust runtime and your PHP code live in the same process. No inter-process protocol between server and worker (we deliberately dropped pipe / fork / MessagePack approaches).
- **Rust under the hood.** Memory safety and predictable performance where it matters — the runtime, the worker pool, the I/O.
- **Framework-agnostic.** First-class adapters for Laravel, Symfony, Spiral, and Yii3.
- **Distribution that just works.** The Rust runtime ships as a pre-built extension via PIE — no Rust toolchain on the target; adapters ship on Packagist.

## What Folk is **not**

To set expectations honestly:

- **Not an AOT compiler.** The Zend VM still runs your PHP — Folk doesn't compile PHP to native code.
- **Not a replacement for PHP.** It's a runtime *for* PHP.
- **Not a framework.** It runs your existing framework; it doesn't replace it.

---

## Built by the folk, written by AI

This is the part that makes Folk unusual, and we'd rather say it up front than bury it.

**Folk is developed by AI agents, under human review, following a spec-driven process.** Contributors don't usually open a pull request with code. They bring a **well-formulated idea or specification** — and the implementation is produced by AI against that spec.

That means the most valuable contribution here is **clear thinking**. A sharp problem statement, a researched design, a precise spec — that's what moves the project. Code is downstream of a good spec.

Why we work this way:

- It keeps the 14-repo workspace coherent — every change follows the same spec → research → implement → verify loop.
- It lowers the barrier to *meaningful* contribution: you don't need to be a Rust expert to shape Folk, you need a good idea expressed clearly.
- It's honest. AI writes the code; humans decide what's worth building and review what ships.

> **Folk** — as in *folk*: of the people, by common minds. A product assembled from the community's ideas and brought to life in code with AI. *Fast PHP, made by common minds.*

## How to contribute

The life of an idea:

```
idea / issue
   → discussion (is this the right problem? the right shape?)
   → spec + research.md (what, why, prior art, trade-offs)
   → implementation by AI (under review)
   → quality gates (see below)
   → release
```

**Bring an idea or a spec, not (necessarily) a patch.** Open an issue in the [`folk-releases`](https://github.com/Folk-Project/folk-releases) tracker. The clearer and more researched it is, the faster it can become real:

- **State the problem** before the solution.
- **Show prior art** — how do RoadRunner/Swoole/FrankenPHP/others handle this?
- **Name the trade-offs** you see.
- **Be concrete** — examples, config snippets, expected behavior.

A good idea, written so an AI agent can implement it without guessing, is the single most useful thing you can give Folk.

## What gets into a release

A change ships only after it clears the gates:

- **Rust:** `fmt` + `clippy` + tests, all green.
- **End-to-end:** a Docker smoke test against a real framework app must pass before anything is published.
- **Review:** a maintainer makes the final call on what's in scope and what ships.

Nothing reaches crates.io or Packagist without passing an end-to-end smoke test first.

## Roadmap

Where Folk is heading (so your ideas can aim at the same target):

- **Embedded mode** — a standalone single-file binary: Rust host + statically linked ZTS PHP + your app, for Go-style deployment.
- **Concurrency** — Fibers / TrueAsync-based per-worker concurrency and response streaming.

## Repositories

Folk is a workspace of focused repositories:

| Repo | What it is |
|---|---|
| `folk-core` / `folk-ext` | The Rust runtime and the PHP extension |
| `folk-api` | Plugin trait / extension API |
| `folk-plugin-*` | HTTP, gRPC, jobs, metrics, process plugins |
| `folk-builder` | Builds the extension `cdylib` for your app |
| `folk-sdk` | PHP SDK (`folk/sdk`) |
| `folk-laravel` / `folk-symfony` / `folk-spiral` / `folk-yii3` | Framework adapters |
| `folk-releases` | Docs, CI, pre-built binaries, **issue tracker** |

## License

- **Rust crates** (`folk-core`, `folk-ext`, plugins, `folk-builder`, …): **Apache-2.0** — including its explicit patent grant.
- **PHP packages** (`folk/sdk`, `folk/laravel`, and the other adapters): **MIT**.

---

*Folk is built in the open. Bring an idea.*
