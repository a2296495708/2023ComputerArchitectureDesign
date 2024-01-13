助教好，这是实现论文复现的相关代码部分。

我复现的项目是用gpgpu-sim来模拟并行基线SIMT加速器，前置要求是gpgpu-sim 4.0

相关依赖安装参考以下github文档:https://github.com/gpgpu-sim/gpgpu-sim_distribution

由于论文使用的是GTX480编译，所以我采取与论文一致的器件进行相关的修改。我复现编译的cu文件存放在.\configs\tested-cfgs\SM2_GTX480中，编译得到的结果也以log文件的形式存放在了同级文件夹。

具体的实现可以参照复现论文。

对于一般的例子，需要展示对基线SIMT的修改，可以运行以下命令来编译运行cu文件

```c
nvcc --cudart shared -arch=sm_30 test_mpipdom_dualpath1.cu -O0 -Xcicc -O0 -Xptxas -O0 -g
nvcc --cudart shared -arch=sm_30 test_mpipdom_dualpath2.cu -O0 -Xcicc -O0 -Xptxas -O0 -g
```

由于dualpath似乎比嵌套分支更容易，所以它作为测试用例会更好。

对于论文中的三个例子，分别运行方式如下：

1. ST表与RT表的实现

   运行以下命令：

   ```c
   nvcc --cudart shared -arch=sm_30 test_mpipdom_st_rt.cu -O0 -Xcicc -O0 -Xptxas -O0 -g
   ```

   运行得到的test_mpipdom_st_rt.log文件放在同级文件夹下

2. scoreboard表逻辑的实现

   由于这个表修改了gpu的内核，所以需要使用test_mpipdom_scoreboard.cu作为主机代码，test_mpipdom_scoreboard.ptx 作为设备内核ptx，然后**运行compileptx.sh**将它们合并并编译成一个fatbinary。

   得到的结果在test_mpipdom_scoreboard.log文件中

   

3. Warp Splits并行数对执行效率影响分析

   与上面一样，需要使用test_mpipdom_nested_branch.cu作为主机代码，test_mpipdom_nested_branch.ptx 作为设备内核ptx，然后**运行compileptx.sh**将它们合并并编译成一个fatbinary。

   对于这个，可能需要更改config文件，设置SP单元，并且更改SIMD的流水线工作模式，在上面的代码中已经默认更改，但若要测试一开始的命令，需要在src中的shader.cc来启用，具体从复现论文中可以知道。

   

   

   ​	

最后是GPGPU论文必须引用部分：

Mahmoud Khairy, Zhesheng Shen, Tor M. Aamodt, Timothy G Rogers. Accel-Sim: An Extensible Simulation Framework for Validated GPU Modeling. In proceedings of the 47th IEEE/ACM International Symposium on Computer Architecture (ISCA), May 29 - June 3, 2020.

https://github.com/gpgpu-sim/gpgpu-sim_distribution