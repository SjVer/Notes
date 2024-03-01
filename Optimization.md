Think like a CPU:
- Instruction pipelines
- Latencies
- Instruction dependencies
- Bandwidth
- Cycles
- Floating point vs integer
- SIMD

What is optimization?
- Big O
- Research: don't reinvent the wheel
- Data characteristics & algorithm choice
- Trust no one (STL, Boost)
- Be as accurate as necessary, but no more

Memory hierarchy:
- Cache architecture
- Cache lines
- Hits, misses and collisions
- Eviction policies
- Prefetching
- Cache-oblivious

**Don't assume, measure!**
- Profilers
- Interpreting profiling data
- Steering the optimization effort

**Premature optimization is the root of all evil!**

## The Approach
Optimization is a deliberate process; you can yield consistent results:
1. Determine optimization requirements: _do we need to?_
2. Profile: _what do we need to optimize?_
3. Analyze hotspots: _why is this slow?_
4. Apply high-level optimizations: _can't we just do less?_
5. Profile again: _did we improve?_
6. Parallelize/vectorize/use GPGPU: _do more work at once._
7. Profile again: _is this fast enough?_
8. Apply low-level optimizations: _squeeze out those pesky cycles._
9. Repeat from step 7 until time runs out.

## Determining the Requirements
The project requirements determine how much you can optimize.

On a project level you're constrained by:
- Time available for optimization
- Constrains related to maintainability/portability

On a low level you're constrained by:
- Target hardware
- Target performance

## Low-Level Optimizations
### Instruction Cost
