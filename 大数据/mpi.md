### 配置（linux）

- 下载[MPICH | High-Performance Portable MPI](https://www.mpich.org/)
- 安装`sudo apt install mpich`
- 编译`mpicxx mpi_hello.cpp -o mpi_hello`
- 运行`mpiexec -n 4 ./mpi_hello`