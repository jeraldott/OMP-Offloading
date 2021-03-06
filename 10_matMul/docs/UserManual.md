---
title: matMul
author: Xin Wu (PC²)
date: 12.03.2020
---

# Introduction

`matMul` performs matrix multiplication in single-precision on GPU. The
performance (in GFLOPS) for different implementations is compared and the
numerical results are also verified.

* Column-major is assumed thru the entire code!

* For testing the dimension of all matrices are assumed to be 4096 x 4096.

* The following table only summarizes the most important points. For more
  details on the ial-th OpenMP GPU implementation see comments in `matMulAB.c`.

| ial |  Remarks                                                               |
|:---:|------------------------------------------------------------------------|
|  0  | jik-loop, 2^9 threads * 2^3 teams,                                     |
|     | uncoalesced memory access                                              |
|  1  | jki-loop, 2^9 threads * 2^3 teams,                                     |
|     | uncoalesced memory access, uncoalesced r&w in innermost loop           |
|  2  | jik-loop, 2^9 threads * 2^f teams, collapse(2)                         |
|  3  | jki-loop, 2^9 threads * 2^f teams, collapse(2),                        |
|     | race condition for writing c!                                          |
|  4  | jik-loop, 2^9 threads * 2^f teams, collapse(2),                        |
|     | 4x k-loop unrolling                                                    |
|  5  | jik-loop, 2^7 threads * 2^f teams, collapse(3),                        |
|     | 4x i-loop unrolling (2x + 2x),                                         |
|     | 4x k-loop unrolling,                                                   |
|     | rb: 4x data reuse                                                      |
|  6  | jik-loop, 2^7 threads * 2^e teams, collapse(3),                        |
|     | 2x j-loop unrolling,                                                   |
|     | 4x i-loop unrolling (2x + 2x),                                         |
|     | 4x k-loop unrolling,                                                   |
|     | ra: 2x data reuse,                                                     |
|     | rb: 4x data reuse                                                      |
|  7  | cublasSgemm in CUBLAS                                                  |

# Usage

```bash
matMul $((2**12))
```

