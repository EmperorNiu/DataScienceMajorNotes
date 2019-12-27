# 第九讲 Integer Programing 整数规划

### 组合优化问题

#### 问题描述

- 可行解
- 目标函数：对每一个可行解，有一个”代价函数“f(x)，衡量可行解的好坏
- 优化问题：
  - 最大化目标函数：$f(\cdot)$往往代表收益
  - 最小化目标函数：$f(\cdot)$往往代表风险

#### 旅行商问题（TSP）

- 问题描述

  给定一些城市，旅行商必须经过所有城市，每个城市间往来的代价不同，要寻找代价最小的回路

- 符号定义

  - 无向图$G=(V,E)$，$V=\{1,\cdots,n\}$
  - $x_{ij}=\begin{cases}1 ,边e_{ij}在所选路径中\\0,\ otherwise\end{cases}$
  - C是代价矩阵，$c_{ij}$代表$i$到$j$的代价

- TSP优化问题描述为
  $$
  Minimize:\; \sum_{i\ne j}c_{ij}x_{ij}\\
  Subject\ to:\sum^n_{j=1}x_{ij}=1\ (i\in V,i\ne j)\\\sum^n_{i=1}x_{ij}=1\ (j\in V,j\ne i)\\\sum_{i,j\in S}x_{ij}\le \lvert S\rvert-1\ (保证只有一个回路),\ 2\le\lvert S\rvert\le n-2,\ S\subset V
  $$

#### 集合覆盖问题（SCP）

- 符号定义
  - 全集：$U=\{u_1,u_2,\cdots,u_n\}$
  - 子集：$S=\{s_i\vert s_i\in U,1\le i\le m\}$
  - 代价：$C=\{c_1,c_2,\cdots,c_m\}$
  - 元素与子集的关系矩阵A，$a_{ij}=\begin{cases}1 ,u_j\in s_i\\0,\ otherwise\end{cases}$
  - $x_{i}=\begin{cases}1 ,选中子集i\\0,\ otherwise\end{cases}$

- 优化目标：

  找到一个集合$I\subset \{1,2,\cdots,m\}$，最小化$\sum_{i\in I}c_i$，使得$\bigcup_{i\in I}s_i=U$
  $$
  Minimize:\; \sum_{i}^mc_{i}x_{i}\\
  Subject\ to:\sum^m_{i=1}x_ia_{ij}\ge 1,\ j\in[1,n]
  $$
  

#### 整数规划

- Input：整数变量$x_1,x_2,\cdots,x_n$，线性不等式，线性等式
- 可行解：解$x'$满足所有等式和不等式且是整数
- 目标函数：maximize $\sum_ic_ix_i$



#### 约束条件的构造举例

##### 0-1规划问题的常见约束写法

- 如果选$x_1$，就不能选$x_2$，则$x_1+x_2\le1$
- 如果选$x_1$，$x_2$一定选，则$x_1\le x_2$
- $x_1,x_2$都要选，则$x_1+x_2>1$

##### 逻辑约束：或

例：约束条件$x\le 2$或$x\ge6$表达为

设$w=x_{i}=\begin{cases}1 ,x\le2\\0,x\ge6\end{cases}$，M为一大数，则整数规划为
$$
x\le2+M(1-w)\\x\ge 6-Mw\\w\in \{0,1\}
$$

- 解释

  如果$x\le2$，则w=1，则上述不等式变为$x\le2且x\ge 6-M$

  如果$x\ge6$，则w=0，则上述不等式变为$x\le2+M且x\ge 6$

- 通解

  对含有$>$的不等式，不等式右边减去Mw（或者M(1-w)，两者等价）

  对含有$<$的不等式，不等式右边加上M(1-w)

  对w的定义与上述加减要相符

#### 分段线性函数建模

$$
y=\begin{cases}
2x,&if\ 0\le x\le3\\
9-x,&if\ 4\le x\le7\\
-5+x,&if\ 8\le x\le 9
\end{cases}
$$

可以改写为
$$
y=2x_1+(9w_2-x_2)+(-5w_3+x_3)
$$
满足约束
$$
0\le x_1\le3w_1,w_1\in\{0,1\}\\
4w_2\le x_2\le7w_2,w_2\in\{0,1\}\\
8w_3\le x_3\le9w_3,w_3\in\{0,1\}\\
w_1+w_2+w_3=1\\
x=x_1+x_2+x_3,x_i是整数
$$

### Branch and Bound

#### 枚举树




