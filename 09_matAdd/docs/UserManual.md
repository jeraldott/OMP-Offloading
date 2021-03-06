---
title: matAdd
author: Xin Wu (PC²)
date: 12.03.2020
---

# Introduction

`matAdd` performs matrix addition (A += B) in single-precision on GPU.
The performance (in GB/s) for different implementations is compared and
the numerical results are also verified.

* Column-major is assumed thru the entire code!

* For testing the dimension of all matrices are assumed to be 4096 x 4096.

* The following table only summarizes the most important points. For more
  details on the ial-th OpenMP GPU implementation see comments in `matAddAB.c`.

| ial |  Remarks                                                               |
|:---:|------------------------------------------------------------------------|
|  0  | ij-loop, 2^9 threads * 2^3 teams,                                      |
|     | coalesced memory access                                                |
|  1  | ji-loop, 2^9 threads * 2^3 teams,                                      |
|     | uncoalesced memory access                                              |
|  2  | ij-loop, 2^9 threads * 2^f teams, collapse(2),                         |
|     | uncoalesced memory access                                              |
|  3  | ji-loop, 2^9 threads * 2^f teams, collapse(2),                         |
|     | coalesced memory access                                                |
|  4  | ji-loop, 2^8 threads * 2^f teams, collapse(3),                         |
|     | 2x i-loop unrolling                                                    |
|  5  | ji-loop, 2^7 threads * 2^f teams, collapse(3),                         |
|     | 4x i-loop unrolling                                                    |
|  6  | ji-loop, 2^7 threads * 2^e teams, collapse(3),                         |
|     | 4x i-loop unrolling, 2x j-loop unrolling                               |
|  7  | cublasSaxpy in CUBLAS                                                  |

# Usage

```bash
matAdd $((2**12))
```

