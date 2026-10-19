# cycle-comb-batch

A Go orchestrator that runs batches of thermoelectric plant simulations in parallel against a FastAPI microservice, and measures how far that parallelism pays off.

> **Status:** work in progress. The results section is intentionally empty and will be filled in after the benchmark runs.

## The question this project answers

> How much faster does a batch of simulations run when executed in parallel, and at what concurrency level does the service stop keeping up?

## Context

The simulation microservice (FastAPI, hosted on Render) exposes two endpoints:

- `GET /substances`: returns the chemical substances used to compose the fuel gas
- `POST /simulation`: takes a JSON input and returns the simulation results, or an error (validation, logical, physical, computational or thermodynamic)

Today, running several simulations means calling them one at a time.

## Scope

**In scope**
- Load a set of simulation payloads (one `.json` file per simulation)
- Run N simulations at the same time, with configurable concurrency (worker pool)
- Classify each outcome: success, domain error or infrastructure error
- Measure total time and per-simulation time
- Handle Render's cold start (warm-up before the batch)

**Out of scope (for now)**
- History persistence (database)
- Integration with MFT or with the .NET management API
- A UI or HTTP endpoints exposed by the orchestrator itself
- Any change to the simulation microservice, which is treated as a black box

## Methodology

The same batch of simulations is executed several times, changing only the concurrency level. Steps: **1 (sequential baseline), 2, 4, 8, 16**. Testing stops when signs of saturation appear.

To keep the measurement reliable:
- Use valid payloads of similar size, so variation between simulations doesn't hide the effect of concurrency
- Repeat each step at least 3 times and record the average
- Warm up the service before each round and only then start the timer
- Separate domain errors (which depend on the input) from infrastructure errors (timeouts, connection refused, 5xx), since only the latter indicate saturation

## Results

Test batch: *(fill in: number of simulations, payload description)*
Environment: *(fill in: Render free tier, local, etc.)*
Measurement date: *(fill in)*

| Concurrency | Total time | Avg time/sim | Max time/sim | Successes | Domain errors | Infra errors | Speedup vs baseline |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| 1 (baseline) | | | | | | | 1.0x |
| 2 | | | | | | | |
| 4 | | | | | | | |
| 8 | | | | | | | |
| 16 | | | | | | | |

**Speedup vs baseline** = baseline total time ÷ total time at that concurrency level.

## Execution plan

- [x] Define scope and methodology
- [ ] Collect the service's `openapi.json` and 5 to 10 valid payloads
- [ ] Go client: run a single simulation and handle errors
- [ ] Worker pool with configurable concurrency
- [ ] Run the benchmark steps and fill in the table
- [ ] Write the conclusions
