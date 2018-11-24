## optimize-gemm-guide
本文是个人在学习**矩阵相乘（GEMM: general matrix to matrix multiplication）优化**后做的一个简单总结。希望对于矩阵乘优化的初学者来说，能有不错的入门引导作用。   

# 矩阵概念 #
相信大家在线性代数的学习过程中都学习过矩阵相乘的概念了，有以下几种情况：   
    &emsp;&emsp;矩阵 \* 矩阵 = 矩阵：M(m\*k) \* M(k\*n) = M(m\*n)  
    &emsp;&emsp;矩阵 \* 向量 = 向量：M(m\*k) \* M(k\*1) = M(m\*1)  
    &emsp;&emsp;向量 \* 矩阵 = 矩阵：M(1\*k) \* M(k\*n) = M(1\*n)  
    &emsp;&emsp;向量 \* 向量 = 矩阵：M(m\*1) \* M(1\*n) = M(m\*n)  
    &emsp;&emsp;向量 \* 向量 = 点：M(1\*k) \* M(k\*1) = m  
除了熟知矩阵相乘的规则外，需要了解一个概念--**分块矩阵**，同时了解**分块矩阵的运算**，这也在线性代数的学习过程中有提到，不过可能不是着重讲解的部分，很多同学就忽略了。然而，这对我们后面要做的矩阵优化来说是非常重要的。*（不过这里的分块与后面how-to-optimize-gemm中应用的分块，以及论文anatomy中讲解的分块略有不同，后面详解）*
# BLAS及其各类实现 #
[BLAS](https://zh.wikipedia.org/wiki/BLAS)(Basic Linear Algebra Subprograms) 是一个应用程序接口（API）标准，用以规范发布基础线性代数操作的数值库（如矢量或者**矩阵乘法**）
