# chipyard spec2006 data collection  

## chipyard

```
git clone -b TMAM git@github.com:KingFrige/chipyard.git
cd chipyard
./scripts/init-submodules-no-riscv-tools.sh

git status

git submodule update <repo-path>
```

- [initial-repository-setup](https://chipyard.readthedocs.io/en/latest/Chipyard-Basics/Initial-Repo-Setup.html#initial-repository-setup)

## build spec2006   

1. 提前安装好spec2006  

2. 使用 [Speckle](https://github.com/KingFrige/Speckle) 脚本编译spec2006   

Speckle fork自[ccelio/Speckle](https://github.com/ccelio/Speckle), 主要参考分支 `spike-sim` 与 `board`  
  - branch `spike-sim` : 给spike功能仿真用  
  - branch `board` : 给FPGA板子使用  

3. Speckle 快速使用  

```
$ ./gen_binaries.sh --compile --copy
```

可以更新 `gen_binaries.sh` 中的 `INPUT_TYPE & SUITE_TYPE` 生成对应的workloads

4. 将需要执行的 workloads 打包到脚本中，可以进行批处理，请参考 [run-spec2006-workload](https://github.com/KingFrige/run-spec2006-workload/blob/main/misc/run-spec2006-tasks.sh)

![](picture/run-spec2006-tasks.png)


## fpga-shell build bitstream  

1. [sdboot initial hpm count](https://github.com/KingFrige/chipyard/tree/TMAM/fpga/src/main/resources/vcu118/sdboot)   

- 使能 `user-mode` 读hpm count权限

```c
write_csr(scounteren, -1);
write_csr(mcounteren, -1);
```

- 配置对应的 hpm count

![](picture/sdboot.png)


2. 使用vivado生成bitstream文件


## [FireMarshal](https://github.com/KingFrige/FireMarshal/tree/perf) build image

1. 配置 [buildroot-config](https://github.com/KingFrige/FireMarshal/blob/perf/boards/prototype/base-workloads/br-base/buildroot-config)，使其包含 `bash`  

```
BR2_SYSTEM_BIN_SH_BASH=y
BR2_SYSTEM_BIN_SH="bash"
BR2_PACKAGE_BUSYBOX_SHOW_OTHERS=y
BR2_PACKAGE_BASH=y
BR2_PACKAGE_READLINE=y
BR2_INIT_BUSYBOX=y
```

2. 使用 [run-spec2006-workload](https://github.com/KingFrige/run-spec2006-workload) 生成对应的 `image` 文件

- 注意确认初始化的脚本 [run-spec2006.sh](https://github.com/KingFrige/run-spec2006-workload/blob/main/overlay/run-spec2006.sh)

3. 将 image 文件写到 `SD card`  

- 按照chipyard文档将 `SD card` 分成两个分区，参考 [setting-up-the-sdcard](https://chipyard.readthedocs.io/en/latest/Prototyping/VCU118.html#setting-up-the-sdcard)

- [quick-start](https://github.com/KingFrige/FireMarshal/blob/perf/quick-start.md)


## `riscv-perf-hpmcounters` read hpm count   

[riscv-perf-hpmcounters](https://github.com/KingFrige/riscv-perf-hpmcounters) fork 自 [riscv-hpmcounters](https://github.com/ccelio/riscv-hpmcounters)

`riscv-perf-hpmcounters` 能在riscv平台上将执行 `workload`，并在执行结束打印 hpm counter的数据


1. 使用方法

统计 `ls -l` 执行的hpm counter信息

```
$ ./riscv-hpmcounters-read "ls -l"
```

## copy spec2006 & `riscv-perf-hpmcounters`  

将 spec2006 和 `riscv-perf-hpmcounters` 拷贝到 `SD card` 的第二个分区上


## setup FPGA   

使用vivado将预先build好的bitstream文件下载到FPGA上，FPGA将自动从SD card boot，并自动开始执行放在卡上的 `workloads`

![](picture/vivado.png)


## analysis data  


执行结束后，将生成的 `*.tma` / `.info` 文件从 SD card上拷贝到 host上处理
使用工具 [riscv-perf-tools](https://github.com/KingFrige/riscv-perf-tools) 进行处理

- TMAM 处理 
- info 处理

![spec2006 data collection](picture/spec2006-run.png)


