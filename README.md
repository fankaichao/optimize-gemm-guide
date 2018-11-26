## optimize-gemm-guide
本文是个人在学习**矩阵相乘（GEMM: general matrix to matrix multiplication）优化**后做的一个简单总结。希望对于矩阵乘优化的初学者来说，能有不错的入门引导作用。   

# 矩阵概念 #
相信大家在线性代数的学习过程中都学习过矩阵的相关概念了。关于矩阵运算，这里我们只关注矩阵乘。有以下几种情况：   
    &emsp;&emsp;矩阵 \* 矩阵 = 矩阵：M<sub>m x k</sub> \* M<sub>k x n</sub> = M<sub>m x n</sub>  
    &emsp;&emsp;矩阵 \* 向量 = 向量：M<sub>m x k</sub> \* M<sub>k x 1</sub> = M<sub>m x 1</sub>  
    &emsp;&emsp;向量 \* 矩阵 = 矩阵：M<sub>1 x k</sub> \* M<sub>k x n</sub> = M<sub>1 x n</sub>  
    &emsp;&emsp;向量 \* 向量 = 矩阵：M<sub>m x 1</sub> \* M<sub>1 x n</sub> = M<sub>m x n</sub>  
    &emsp;&emsp;向量 \* 向量 = 点：M<sub>1 x k</sub> \* M<sub>k x 1</sub> = m  
除了熟知矩阵相乘的规则外，需要熟知一个概念--[分块矩阵](https://zh.wikipedia.org/wiki/%E5%88%86%E5%A1%8A%E7%9F%A9%E9%99%A3)，熟知分块矩阵的运算，这也在线性代数的学习过程中有提到，不过可能不是着重讲解的部分，很多同学就忽略了。然而，这对我们后面要做的矩阵优化来说是非常重要的。*（不过这里的分块与后面how-to-optimize-gemm中应用的分块，以及论文anatomy中讲解的分块略有不同，后面详解）*

# BLAS及其各类实现 #
[BLAS](https://zh.wikipedia.org/wiki/BLAS)(Basic Linear Algebra Subprograms) 是一个应用程序接口（API）标准，用以规范发布基础线性代数操作的数值库（如矢量或者**矩阵乘法**）。而BLAS的具体实现有很多且方法不同，分为商业和开源两种：
- 商业
    * Intel MKL
    * AMD ACML
    * NVIDIA CUBLAS
    * IBM ESSL  
Intel、AMD、NVIDIA、IBM等公司实现的商业版本都是为了搭配自己的硬件，根据自己的硬件做出了实现以及更好的优化。
- 开源
    * GotoBLAS（2010年中止开发维护）
    * ATLAS
    * OpenBLAS
    * BLIS  
之前比较之名的是GotoBLAS，但是在主力开发人员后藤离开了UT Austin的研究组，2010年就终止开发了。ATLAS是美国一个学校做的。[OpenBLAS](https://github.com/xianyi/OpenBLAS)是PerfXLab澎峰科技创始人张先轶带领团队基于GotoBLAS做的矩阵计算库。而[BLIS](https://github.com/flame/blis)也是基于GotoBLAS扩展出来的一个项目。（说一下，对于OpenBLAS澎峰已经有跟为快速的商业版本，在2018年HPC青岛澎峰科技的展台交流了解到的）  

# GEMM优化 学习步骤 #
这一趴是我个人的总结，如果有更好的入门步骤，可以共享一下。首先明确**矩阵乘**只是BLAS规范的各类数值库中的一部分。  
  
1. 学习、复习上文提到的矩阵的相关概念，矩阵运算，同时熟练使用分块矩阵来对完整矩阵进行运算  

2. 概读论文[Anatomy of High-Performance Matrix](https://dl.acm.org/citation.cfm?id=1356053)  
    弄明白什么是分块，如何分块，以及为什么分块（即矩阵分块与计算机体系结构中存储器层次结构的关系）  
    
3. 跟随开源项目[how-to-optimize-gemm](https://github.com/flame/how-to-optimize-gemm)中的指导，一步一步地尝试实现  
    在这一阶段，该项目一步一步引导着提供了各类优化的方向和思路，有的会有性能提升，有的没有，不要只看，一定要自己实现一下（会有一些很小的细节挺有意思的）。然后和“普通三层循环实现”对比一下**性能**，同时关注每一步的优化相对于上一步的性能提升有多少。  
***这一步中，有一个词“性能”需要注意，我们一般用flops来评价，floating point operations per second (FLOPS, flops or flop/s)，每秒浮点运算***  

4. 扩展：how-to-optimize-gemm中向量化使用的指令是128位的，修改扩展为256位版本（或者512位版本）

# 备注 #
1. GEMM优化 学习步骤，第2步，其他参考文献[BLISlab: A Sandbox for Optimizing GEMM](https://arxiv.org/abs/1609.00076)，更多参考文献可见[BLAS](https://zh.wikipedia.org/wiki/BLAS)维基百科
2. GEMM优化 学习步骤，第3步，其他参考开源项目参考[BLISlab](https://github.com/flame/blislab)
3. GEMM优化 学习步骤，第3步，此处提供一篇文章，[OpenBLAS项目与矩阵乘法优化](https://www.leiphone.com/news/201704/Puevv3ZWxn0heoEv.html)，是张先轶根据how-to-optimize-gemm写的一篇指导文，如果看完了第2步中的论文还不是很清晰，可以先看下本文指导  
