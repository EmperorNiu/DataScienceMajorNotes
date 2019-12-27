## 最小二成问题的QR分解法

### 一、QR分解法
$$
\lVert Ax-b \rVert_2 = \min_{z\in C^n} \lVert Az-b \rVert_2\\
\lVert Q^T(Ax-b) \rVert_2^2=\lVert Rx-f_1 \rVert_2^2 + \lVert f_2\rVert_2^2\\
其中 f=Q^Tb=\begin{bmatrix}
    Q_1^T\\Q_2^T
\end{bmatrix}b=\begin{bmatrix}
    f_1\\f_2
\end{bmatrix}\\
Rx=f_1
$$
对$\hat{A}=[A,b]$进行QR分解
$$
H_n\dots H_1[A,b]=[R,\hat{b}]\\
\Rightarrow Q^T[A,b]=[R,\hat{b}]\\
A=QR,\hat{b}=Q^Tb,Q=H_1\dots H_n
$$
所以对于列满秩矩阵$A\in R^{m*n}$，LS问题QR分解方法将问题转化为求解
$$
Rx=f_1
$$
可先进行QR分解，得到此方程，再用回代法求解$x_{LS}$

### 二、求解秩亏最小二乘问题的数值解法

考虑 $\min \lVert Ax-b\rVert_2$，其中$A\in R^{m*n}_r(m\ge n)$,$rank(A)=r<n$。此时最小二乘问题有无穷个解。
#### 1. 列主元QR分解法
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
