# arm_hgemm
hgemm for arm cortex A55

This is a hgemm implementation on arm A55

Test environment: arm cortex A55 @1.6GHz

theoretical peak performance: 1.6 * (8 * 2) = 25.6Gflops

test result: 24.5 Gflops(95%) when not memory bound, 20.0 Gflops(80%) when normal circumstances


=============================================================================================================

#对于arm计算加速的一点套路

数据不连续? 那就重排变成连续的;

矩阵太大? 那就block一小块一小块的做;

从内存读带宽太小? 那就提高cache利用率不做重复读存到cache;

一次计算一个太少? 那就用simd一次8个，或者npu一次算128个fp16;

读feature map太慢? 那就重排NC1HWC0连续的;

filter读得慢? 那就想要啥样的存成啥样的;

流水线有依赖? 那就乒乓操作，保证计算的流水线打满。

上面这些操作还是太慢

那就把上面操作都换成硬件，如img2col;隔几个读取就行了呗。

还需要专门能计算矩阵相乘的电路，具体来说就是把最主要的矩阵相乘用电路做出来，可以用PE = 16个乘法器和15个加法器和一个激活部分，来做成加法树，很多个PE就可以计算庞大的矩阵啦。还可以用FPGA写个电路出来，或者把矩阵相乘这部分kernel给其他设备，如GPU/dsp

