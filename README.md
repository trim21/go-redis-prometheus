# go-redis-prometheus

[![](https://img.shields.io/github/actions/workflow/status/trim21/go-redis-prometheus/test.yaml?branch=master)](https://github.com/trim21/go-redis-prometheus/actions/workflows/test.yaml)
[![Go Reference](https://pkg.go.dev/badge/github.com/trim21/go-redis-prometheus.svg)](https://pkg.go.dev/github.com/trim21/go-redis-prometheus)


[go-redis](https://github.com/redis/go-redis) hook that exports Prometheus metrics.

## Installation

    go get github.com/trim21/go-redis-prometheus

## Usage

```golang
package main

import (
    redis "github.com/redis/go-redis/v9"
	redisprom "github.com/trim21/go-redis-prometheus"
)

func main() {
    hook := redisprom.NewHook(
        redisprom.WithInstanceName("cache"),
        redisprom.WithNamespace("my_namespace"),
        redisprom.WithDurationBuckets([]float64{.001, .005, .01}),
    )

    client := redis.NewClient(&redis.Options{
        Addr:     "localhost:6379",
        Password: "",
    })
    client.AddHook(hook)

    // run redis commands...
}
```

## Exported metrics

The hook exports the following metrics:

- Single commands (not pipelined):
  - Histogram of commands: `redis_single_commands_bucket{instance="main",command="get"}`
  - Counter of errors: `redis_single_errors{instance="main",command="get"}`
 - Pipelined commands:
   - Counter of commands: `redis_pipelined_commands{instance="main",command="get"}`
   - Counter of errors: `redis_pipelined_errors{instance="main",command="get"}`

## Note on pipelines

It isn't possible to measure the duration of individual
pipelined commands, but the duration of the pipeline itself is calculated and
exported as a pseudo-command called "pipeline" under the single command metric.

## API stability

The API is unstable at this point and it might change before `v1.0.0` is released.
