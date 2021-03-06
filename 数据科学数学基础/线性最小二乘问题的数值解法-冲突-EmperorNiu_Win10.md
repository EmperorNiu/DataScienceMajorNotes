# 线性最小二乘问题的数值解法

> 这里我们讨论的问题主要是Ax=b，$b\notin R(A)$时的处理方法

## 最小二乘问题的描述

### 方程组的分类

对于方程组
$$
Ax=b,A\in C^{m\times n},b\in C^m\tag{1}
$$

- 超定方程组：m>n
- 欠定方程组：m<n

### 线性最小二乘问题的基本概念

#### 最小二乘问题的特征及一般表示

- 最小二乘问题

  设$A\in C^{m\times n},b\in C^m$，确定$x\in C^n$使得
  $$
  \lVert Ax-b\rVert_2=\min_{x\in C^n}\lVert Ax-b\rVert_2 \tag{2}
  $$
  

  问题(2)称为线性最小二乘问题（LS问题），而x称为最小二乘解。称$r(x)=b-Ax$为残差向量

- 所有最小二乘解的集合记为$S_{LS}$
  $$
  S_{LS}=\{x\in C^n:x满足(1)\}\tag{3}
  $$
  

  $S_{LS}$中2-范数最小者称为极小范数解，记为$x_{LS}$
  $$
  \lVert x_{LS}\rVert_2=\min\{\lVert x\rVert_2:x\in S_{LS}\}\tag{4}
  $$
  
- 极小范数解：方程组(1)有解的情况
  $$
  \lVert x_{0}\rVert_2=\min_{Ax=b}\lVert x\rVert_2\tag{5}
  $$

- 极小范数最小二乘解：方程组无解的情况
  $$
  \lVert x_{0}\rVert_2=\min_{min\lVert Ax-b\rVert_2}\lVert x\rVert_2\tag{6}
  $$

- 线性方程组(1)有解的充要条件是
  $$
  AA^{\dagger}b=b\tag{7}
  $$
  并且在有解时，其通解为
  $$
  x=A^{\dagger}b+(I-A^{\dagger}A)z\tag{8}\\其中z\in C^n任意
  $$
  证明：

  “必要性”：

  若$AA^{\dagger}b=b$，则$x=A^{\dagger}b$就是其解

  "充分性"

  若$Ax=b$有解，则$N(A)=\{(I_n-A^{\dagger}A)z:z\in C^n\}$

- 如果方程组(1)有解，则它的极小范数解$x_0$唯一，并且$x_0=A^{\dagger}b$

#### 线性最小二乘问题的等价性问题

##### 法方程

- x是最小二乘问题(2)的极小解的，即$x\in S_{LS}$的充分必要条件是x为方程

  $$
  A^HAx=A^Hb\tag{9}
  $$

​		的解，(9)式称为最小二乘问题的法方程。

- 证明：

  $\min_x\lVert Ax-b\rVert_2$等价于$\min_x\lVert Ax-b\rVert_2^2=\min_x(Ax-b)^T(Ax-b)$

  化简得，$\min_x\{x^TA^TAx-x^TA^Tb-b^TAx+b^Tb\}$。设$y=x^TA^TAx-x^TA^Tb-b^TAx+b^Tb$，
  令$\dfrac{dy}{dx}=0$得，
  $$
  2A^TAx-2A^Tb=0\\
  	A^TAx=A^Tb
  $$
  所以，(2)式的极小解为$A^HAx=A^Hb$的解

##### KKT方程

- 设$A\in C^{m\times n}$，$b\in R^n$，则$x$和$r=b-Ax$分别是最小二乘问题(2)的极小解和残量的充分必要条件是x和r为鞍点系统
  $$
  \begin{bmatrix}I&A\\A^H&O\end{bmatrix}
  \begin{bmatrix}r\\x\end{bmatrix}=
  \begin{bmatrix}b\\0\end{bmatrix}\tag{10}
  $$
  的解。上述线性系统称为最小二乘问题的KKT方程

- 证明：

  若$x$为最小二乘问题(2)的极小解，而$r=b-Ax$为其残量，则
  $$
  x=A^{\dagger}b+(I-A^{\dagger}A)z\\
  r=b-Ax=b-AA^{\dagger}b=(I-AA^{\dagger})b
  $$
  由广义逆$A^\dagger$的性质$AA^\dagger=(AA^\dagger)^T,AA^\dagger A=A$及等式$r+Ax=b$，得
  $$
  A^Hr=A^H(I-AA^\dagger)b=[(I-AA^\dagger)A]^Hb=[A-AA^\dagger A]^Hb=0
  $$
  所以(10)式是相容的线性系统，且x和r满足式(10).

  反之，设
  $$
  B=\begin{bmatrix}I&A\\A^H&O\end{bmatrix}
  $$
  可以验证
  $$
  B^{\dagger}=\begin{bmatrix}I-AA^{\dagger}&(A^\dagger)^H\\A^H&-A^\dagger(A^\dagger)^H\end{bmatrix}
  $$
  所以
  $$
  \begin{bmatrix}r\\x\end{bmatrix}=B^\dagger\begin{bmatrix}b\\0\end{bmatrix}+
  (I-B^\dagger B)\begin{bmatrix}y\\z\end{bmatrix}=
  \begin{bmatrix}(I-AA^\dagger)b\\
  A^\dagger b-(I-A^\dagger A)z\end{bmatrix}
  $$
  所以r,x分别是最小二乘问题的残量和极小解

#### 线性最小二乘问题的正则化

设矩阵$A\in C_r^{m\times n}$的奇异值分解为
$$
A=U\Sigma V^H
$$
其中$U=[u_1,u_2,\cdots,u_m]$为m阶酉矩阵；$V=[v_1,v_2,\cdots,v_n]$为n阶酉矩阵；$\Sigma=\begin{bmatrix}\Sigma_r&0\\0&0\end{bmatrix}$

则最小二乘问题(2)的极小范数最小二乘解$x_{LS}$可表示为
$$
x_{LS}=A^\dagger b=\sum^r_{i=1}\dfrac{u_i^Hb}{\sigma_i}v_i
$$

## 最小二乘问题的求解

### 求解列满秩最小二乘问题的数值方法

#### 法方程方法

##### 分析

将最小二乘问题化为上文所述的法方程
$$
A^HAx=A^Hb
$$
由于$A\in C^{m\times n}$是列满秩矩阵，所以$rank(A^HA)=n$，且$A^HA$正定，所以可以用Cholesky分解求解方程

##### 算法：

- 对n阶Hermite正定矩阵$B=A^HA$作Cholesky分解$B=LL^H$，其中L为下三角矩阵 
- 依次解$Ly=A^Hb$，$L^Hx=y$得到最小二乘问题的解$x_{LS}$

#### QR分解法

##### 分析

由于正交矩阵保持向量二范数不变，所以可以对最小二乘问题做变换
$$
\min_x\lVert Ax-b\rVert\Rightarrow\min_x\lVert Q^T(Ax-b)\rVert\\其中Q为正交矩阵
$$
所以可以找一个合适的Q使原来的最小二乘问题便于求解。

设A有QR分解：
$$
A=Q\begin{bmatrix}R\\0\end{bmatrix}=Q_1R\\其中Q\in R^{m\times n}为正交矩阵\\Q_1为Q的前n列组成的矩阵\\R为对角元均为正的上三角矩阵
$$
取上式中的Q对b做变换得
$$
f=Q^Tb=\begin{bmatrix}Q_1^T\\Q_2^T\end{bmatrix}b=\begin{bmatrix}f_1\\f_2\end{bmatrix}
$$
则
$$
\lVert Q^T(Ax-b) \rVert_2^2=\Bigg\lVert \begin{bmatrix}R\\0\end{bmatrix}x-Q^Tb \Bigg\rVert_2^2 \\
=\Bigg\lVert \begin{bmatrix}R\\0\end{bmatrix}x-\begin{bmatrix}f_1\\f_2\end{bmatrix} \Bigg\rVert_2^2= 
\lVert Rx-f_1 \rVert_2^2+\lVert f_2\rVert_2^2\\
$$
由此可知，x是最小二乘问题的解，当且仅当x是上三角矩阵方程组$Rx=f_1$的解

在实施过程中，采取对$\hat{A}=[A,b]$进行QR分解
$$
H_n\dots H_1[A,b]=[R,\hat{b}]\Rightarrow Q^T[A,b]=[R,\hat{b}]
$$
得
$$
A=QR,\hat{b}=Q^Tb
$$

##### 算法

给定$A\in R^{m\times n}$为列满秩矩阵，$b\in R^m$

- 计算增广矩阵$\hat{A}=[A,b]$的QR分解
- 用回代法求解$Rx=f_1$

QR分解可以用

- Householder方法
- Givens正交化方法
- 修正的Gram-Schmit方法

### 求解秩亏最小二乘问题的数值解法

考虑 $\min \lVert Ax-b\rVert_2$，其中$A\in R^{m*n}_r(m\ge n)$,$rank(A)=r<n$。此时最小二乘问题有无穷个解。
#### 列主元QR分解法

##### 分析

A是秩亏损时，QR分解不一定能给出列空间R(A)的一组标准正交基，但是我们可以通过计算列置换之后的矩阵$\hat{A}=AP$的QR分解来求解来求解，即AP=QR，其中P是置换矩阵
设$b=b_1+b_2,b_1\in R(A),b_2\in R^{\bot}(A)$
可以证明：$Ax=b_1$
现在假定$R(A)=R(Q_1)$其中$Q_1\in R^{m*r}$且$Q^TQ=I_r$，即$Q_1$构成R(A)的一组标准正交基，则存在矩阵$S\in R^{r*n}$和向量$h\in R^r$，使得
$$
A=Q_1S,b_1=Q_1h\\
又\because Ax=b_1\\
\therefore Sx=h
$$
显然方程组总是有解的，进一步可知
$$
S=Q_1^TA,\\h=Q_1^Tb_1=Q_1^T(b-b_2)=Q_1^Tb\\
    b_2\in R^{\bot}(Q_1)
$$
由于rank(A)=r < n,一般不能直接产生R(A)的一组标准正交基，但是如果对A进行适当的排列使其前r列线性无关，然后再进行QR分解，则仍然可产生R(A)的一组标准正交基
设有分解式
$$
    AP=Q\begin{bmatrix}
        R_{11} & R_{12}\\
        0 & 0
    \end{bmatrix}
$$
其中P为置换矩阵，Q为正交矩阵，$R_{11}$为非奇异上三角阵，则Q的前r列就是R(A)的一组标准正交基
一旦求出分解式，就有
$$
(Q^TAP)(P^Tx)=Q^Tb_1\\
    \begin{bmatrix}
        R_{11} & R_{12}\\
        0 & 0
    \end{bmatrix}
    \begin{bmatrix}
        w\\z
    \end{bmatrix}=Q^Tb_1=\begin{bmatrix}
       Q_1^T\\Q_2^T
    \end{bmatrix}b_1=
    \begin{bmatrix}
        h\\g
    \end{bmatrix}\\
    \Rightarrow
    \begin{bmatrix}
        R_{11}w + R_{12}z\\0
    \end{bmatrix}=\begin{bmatrix}
        h\\g
    \end{bmatrix}\\
    \therefore R_{11}w+R_{12}z=h,g=0\\
    w=R_{11}^{-1}(h-R_{12}z),g=0\\
$$

$$
x=P\begin{bmatrix}
        w\\z
    \end{bmatrix}=P\begin{bmatrix}
        R_{11}^{-1}(h-R_{12}z)\\z
    \end{bmatrix}=P\begin{bmatrix}
         R_{11}^{-1}h\\0
    \end{bmatrix}+\begin{bmatrix}
         -R_{11}^{-1}R_{12}\\I_{n-r}
    \end{bmatrix}z\\
$$

$$
令x_b=P\begin{bmatrix}
         R_{11}^{-1}h\\0
    \end{bmatrix}
$$
$x$就是最小二乘基本解
$$
Ax_b=\begin{bmatrix}
        b_1\\0
    \end{bmatrix}
$$

##### 求解P

考虑分解式(8)的具体实施过程。假设对对某一正整数k，已求k-1个Householder变换$H_1,H_2,\cdots ,H_{k-1}$和k-1个初等变换，$P_1,P_2,\cdots,P_{k-1}$使得

$R_{k-1}=H_{k-1}\cdots H_2H_1AP_1P_2\cdots P_{k-1}=\begin{bmatrix}
         R_{11}^{(k-1)} & R_{12}^{(k-1)} \\0 & R_{22}^{(k-1)} 
    \end{bmatrix}$，其中$R_{11}^{(k-1)} $为非奇异上三角矩阵，记$R_{22}^{(k-1)}=[V_k^{(k-1)},V_{k+1}^{(k-1)}，\cdots ,V_n^{(k-1)}] $，其中$V_i^{(k-1)}$表示$R_{22}^{(k-1)}$的第（i-k+1)列，下一步是确定指标P($k\le P \le n$)满足

$\lVert V_p^{(k-1)}\rVert_2=\max \{\lVert V_k^{(k-1)}\rVert_2,\lVert V_{k+1}^{(k-1)}\rVert_2,\cdots,\lVert V_ n^{(k-1)}\rVert_2\}$

若$\lVert V_p^{(k-1)}\rVert_2=0$，停止计算。否则取$P_k$为第k列与第p列交换的初等列变换矩阵，并确定一个Householder变换，$\hat{H}_k\in R^{(m-k+1)\times (m-k+1)}$使得$\hat{H}_kV_p^{(k-1)}=\gamma_{kk}e_1$

令$H_k=diag(I_{k-1},\hat{H}_k)$，则有$R_k=\begin{bmatrix}R_{11}^{(k)} & R_{12}^{(k)} \\0 & R_{22}^{(k)}\end{bmatrix}$，其中$R_{11}^{(k-1)} $为非奇异上三角矩阵$R_k=H_k\begin{bmatrix}R_{11}^{(k-1)} & R_{12}^{(k-1)} \\0 & R_{22}^{(k-1)}\end{bmatrix}P_k$

$R_{12}$约化为0矩阵，$z_1,z_2,\cdots,z_r$和置换，$\hat{P}$，使得$[R_{11},\cdots,R_{1r}]z_1\cdots z_r\hat{P}=[T,0]$，T为上三角矩阵，令

$Z=Pz_1\cdots z_r$，则有$Q^TAZ=\begin{bmatrix}T & 0 \\0 & 0\end{bmatrix}$，由此可得$x_{LS}=z\begin{bmatrix}T^{-1}C \\0 \end{bmatrix}$，C为由$Q^Tb$前r个分量构成的r维向量

##### 算法

秩亏最小二乘问题的列主元QR分解法

- Step1：输入矩阵A，计算$P(j):=j，\sigma_j=A(:j)^TA(:j)$，置k=1
- Step2： 确定r使满足$\sigma_r:\max_{k\le j\le n}\sigma_j$，若$\sigma_r=0$停算
- Step3：交换$P(r)$与$P(k)$，$\sigma_r$与$\sigma_k$以及A(:,r)与A(:,k)
- Step4：确定一个Householder矩阵$\hat{H}_k$使得$\hat{H}_rA(k:m,k)=\gamma_{kk}e_1$并计算$[A,b]:=diag(I_{k-1},\hat{H}_{k-1})[A,b],\sigma_j:=\sigma_j-a_{kj}^2$
- Step5：置k:=k+1，转步骤2

变换技巧
$$
\lVert Q^Tx\rVert_2^2=\lVert [\alpha,z]^T\rVert_2^2=\alpha^2+\lVert z\rVert_2^2\\
\lVert z\rVert_2^2=\lVert x\rVert_2^2-\alpha^2\\
\therefore \lVert x^{(j)}\rVert_2^2=\lVert x^{(j-1)}\rVert_2^2-\alpha^2_{kj}
$$

#### 二、奇异值分解法

设$A\in R^{m\times n}(m>n)$的奇异值分解为$A=U\Sigma V^T$,$\sigma=\begin{bmatrix}\Sigma_r & 0 \\0 & 0\end{bmatrix}$，其中$U=[u_1,\cdots,u_m],V=[v_1,\cdots,v_n],\Sigma_r=diag(\sigma_1,\cdots,\sigma_r)$，则有
$$
x_{LS}=A^+b=V\begin{bmatrix}\Sigma_r^{-1} & 0 \\0 & 0\end{bmatrix}U^T\\
=\sum^r_{i=1}\dfrac{u_i^Tb}{\sigma_i}v_i
$$


### 参考文献

**马昌凤 柯艺芬.数值线性代数与算法[M].国防工业出版社:北京,2017:247-279.**