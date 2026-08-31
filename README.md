<div align="center">

# Harsh Dalmia

Backend · Systems · Developer Tools

</div>

<br>

I'm a Computer Science student at VIT. I mostly work on backend systems and projects where the failure modes are more interesting than the happy path.

Recently, that has meant building an AML investigation system that can justify every number it produces, debugging a Windows process termination path that added five seconds to every reload, and writing a concurrent matching engine where orders, state transitions, and recovery all have to agree.

---

## Things I've built

### NEXUS-AML

**An investigation system for suspicious activity, not just another risk score.**

NEXUS starts with a question and decides what evidence it actually needs. It builds competing suspicious and benign hypotheses, runs the relevant tools, and produces a recommendation backed by an evidence ledger.

The part I care about most is that the LLM does not get to decide whether something is risky. It parses the request and helps narrate the result, but scoring is deterministic.

Some things that came out of building it:

* 5.07M transactions and 515K accounts loaded into DuckDB
* database round-trips reduced from 89 to 11 by batching work
* suspicious and benign explanations tested against competing hypotheses
* every reported number checked against its source transactions
* a nondeterministic DuckDB join bug found and locked down with a 389-test regression suite

`Python` · `FastAPI` · `DuckDB` · `scikit-learn` · `React`

**→ github.com/harshdalmia/nexus**

---

### hotreload

**A cross-platform hot-reload CLI written in Go.**

The basic idea is simple:

`watch → debounce → build → restart`

Most of the work was dealing with what happens when that pipeline goes wrong.

A few examples:

* a restart was being mistaken for a crash because exit detection happened after stopping the process
* Windows was spending five seconds waiting for a process termination path that was never going to succeed gracefully
* cancelling a stale build could leave the application with no server running
* ignore rules worked locally but caused every file event to disappear under Linux CI

The Windows issue was particularly satisfying: the reload path went from **5 seconds to roughly 19ms** after fixing process-group termination.

The controller also has end-to-end tests that run real builds and real processes, including the case where a broken build must leave the previous server running.

`Go` · `Concurrency` · `CLI` · `Process Management`

**→ github.com/harshdalmia/hotreload-tool**

---

### Matching Engine

**A low-latency order matching engine built in Go.**

The engine implements price-time priority and handles more than just matching two orders.

It includes:

* `LIMIT`, `MARKET`, and `STOP` orders
* `GTC`, `IOC`, and `FOK` time-in-force behaviour
* partial fills and explicit order state transitions
* one matching goroutine per instrument
* buffered order intake with backpressure
* Server-Sent Events for live trades and market data
* an append-only write-ahead log for recovery
* a Next.js trading terminal

The matching path is deliberately serialized per symbol. Instead of putting locks around the order book, orders flow through a channel to a single owner of that state.

The test suite includes property tests and replay-equivalence testing to check that recovery produces the same engine state.

Stress testing with 10,000 orders sustained roughly **5,379 orders per second**.

`Go` · `Concurrency` · `HTTP` · `Next.js`

**→ github.com/harshdalmia/market-matching-engine**

---

## Experience

### Software Development Intern — MyPerro

Worked on backend engineering with Node.js, Express, and PostgreSQL.

Some of my work involved REST APIs, OAuth, session management, and caching. Query response times improved by around **25%**, while average latency was reduced from roughly **300ms to 180ms**.

I was also involved in engineering and product discussions around how features should actually be built rather than only implementing predefined requirements.

---

## Stack

**Languages**

Go · Python · TypeScript · JavaScript · Java · C/C++ · SQL

**Backend**

Node.js · Express · FastAPI · PostgreSQL · Redis · DuckDB · MongoDB

**Frontend**

React · Next.js · Redux · Tailwind CSS

**Infrastructure**

Docker · AWS · Git · GitHub

---

## Currently interested in

```text
backend systems
concurrency
distributed systems
databases
developer tools
reliable AI systems
```

I tend to gravitate toward projects where you eventually have to answer questions like:

> What happens if this runs twice?

> What happens if the process doesn't stop?

> What happens if two things modify the same state?

> How do we know this output is actually correct?

Those questions usually turn out to be the interesting part.

---

<div align="center">

Building things, breaking assumptions, then figuring out why they broke.

</div>
