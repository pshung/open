# Using RISC-V intrinsic function to program and run with spike.

Using the RISC-V intrinsic functions to program a vector-add kernel.
Those intrinsic functions have been proposed by [EPI](https://repo.hca.bsc.es/gitlab/rferrer/epi-builtins-ref/-/blob/master/epi-builtins-ref.md).

```
void add_vec(long N, int* C, int* A, int* B) {
      long i;
      for (i = 0; i < N;) {
        long gvl = __epi_vsetvl(N - i, vtype_e32, vtype_m2);
        __epi_m2i32_t  a = __epi_m2i32_vlwv(&A[i]);
        __epi_m2i32_t  b = __epi_m2i32_vlwv(&B[i]);
        __epi_m2i32_t  c = __epi_m2i32_vaddvv(a, b);
        __epi_m2i32_vswv(c, &C[i]);
        i += gvl;
      }
}

```

> clang --target=riscv64 -march=rv64imfdv vec_add.c   -c -o vec_add.o
> riscv64-unknown-elf-gcc -march=rv64imfdv vec_add.o -o vec_add -static
> spike --isa=rv64imafdcv ../riscv64-unknown-elf/bin/pk vec_add

