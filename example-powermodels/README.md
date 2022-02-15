# example-powersystems

### Setup

- Install [Julia](https://julialang.org/downloads/)
- Run the following in a terminal:

  ```
  git clone https://github.com/G-PST/power-flow-exercise
  cd power-flow-exercise/PowerModelsExample
  julia --project -e "using Pkg; Pkg.instantiate()"
  julia --project
  ```

### Performance benchmarks

Benchmarks for loading the system, solving the model, and writing the results:

```julia
julia> using PowerModelsExample

julia> using BenchmarkTools

julia> @btime load_solve_output()
  48.689 ms (408695 allocations: 20.76 MiB)
```

```julia
julia> @benchmark load_solve_output()
BenchmarkTools.Trial: 95 samples with 1 evaluation.
 Range (min … max):  48.746 ms … 63.871 ms  ┊ GC (min … max): 0.00% … 16.00%
 Time  (median):     49.778 ms              ┊ GC (median):    0.00%
 Time  (mean ± σ):   52.684 ms ±  4.774 ms  ┊ GC (mean ± σ):  5.53% ±  7.10%

  █▂▅ ▃                                      ▃
  █████▆▆▄▁▁▃▁▁▃▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▆█▄▆▁▃▁▁▃▁▁▃▃▄▃▃▄ ▁
  48.7 ms         Histogram: frequency by time        61.7 ms <

 Memory estimate: 20.76 MiB, allocs estimate: 408695.
```

### Validation

```julia
julia> compare_v_gen_load()
std(powermodels.V - matpower.V) = 9.475197011613159e-12
std(real.(powermodels.V) - real.(matpower.V)) = 5.7772363578856695e-12
std(imag.(powermodels.V) - imag.(matpower.V)) = 7.510186314200657e-12
std(powermodels.gen - matpower.gen) = 3.765891665794937e-9
std(real.(powermodels.gen) - real.(matpower.gen)) = 1.6551677630946559e-9
std(imag.(powermodels.gen) - imag.(matpower.gen)) = 3.3826557191821934e-9
std(powermodels.load - matpower.load) = 7.802279639429094e-16
std(real.(powermodels.load) - real.(matpower.load)) = 0.0
std(imag.(powermodels.load) - imag.(matpower.load)) = 7.802279639429094e-16

std(abs.(powermodels.V - matpower.V)) = 9.027039524217863e-12
std(abs.(powermodels.gen - matpower.gen)) = 3.7019910273188886e-9
std(abs.(powermodels.load - matpower.load)) = 7.802279639429094e-16
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
                                        Voltage
                      ┌                                        ┐
   [0.0    , 5.0e-12) ┤████████████████████████████████████  29
   [5.0e-12, 1.0e-11) ┤███████████████████████▋ 19
   [1.0e-11, 1.5e-11) ┤█████████████▋ 11
   [1.5e-11, 2.0e-11) ┤████▉ 4
   [2.0e-11, 2.5e-11) ┤███▋ 3
   [2.5e-11, 3.0e-11) ┤███▋ 3
   [3.0e-11, 3.5e-11) ┤██▌ 2
   [3.5e-11, 4.0e-11) ┤██▌ 2
                      └                                        ┘
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
                                    Generation
                    ┌                                        ┐
   [0.0   , 5.0e-9) ┤████████████████████████████████████  65
   [5.0e-9, 1.0e-8) ┤██▎ 4
   [1.0e-8, 1.5e-8) ┤█▋ 3
   [1.5e-8, 2.0e-8) ┤▌ 1
                    └                                        ┘
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
                                         Load
                      ┌                                        ┐
   [0.0    , 5.0e-16) ┤████████████████████████████████████  67
   [5.0e-16, 1.0e-15) ┤  0
   [1.0e-15, 1.5e-15) ┤  0
   [1.5e-15, 2.0e-15) ┤█▋ 3
   [2.0e-15, 2.5e-15) ┤  0
   [2.5e-15, 3.0e-15) ┤  0
   [3.0e-15, 3.5e-15) ┤  0
   [3.5e-15, 4.0e-15) ┤█▋ 3
                      └                                        ┘
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
                      Voltage
    ┌                                        ┐
     ╷  ┌─┬──────┐                         ╷
     ├──┤ │      ├─────────────────────────┤
     ╵  └─┴──────┘                         ╵
    └                                        ┘
     0               2.0e-11          4.0e-11
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
                    Generation
    ┌                                        ┐
     ┬┐                                  ╷
     │├──────────────────────────────────┤
     ┴┘                                  ╵
    └                                        ┘
     0               1.0e-8            2.0e-8
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
                       Load
    ┌                                        ┐
     ┐                                  ╷
     ├──────────────────────────────────┤
     ┘                                  ╵
    └                                        ┘
     0               2.0e-15          4.0e-15
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

```