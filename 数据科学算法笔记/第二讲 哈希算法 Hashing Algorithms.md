# 第二讲 哈希算法 Hashing Algorithms

### 一、哈希函数 Hash function h：

#### 定义

$$
h:key \rightarrow value.i.e.,h(key) = value \in Z^+
$$

#### 特点

- Compression：定义域的基数往往大于值域的基数，所以它由压缩输入的数据的功能

- Resistant to collisions: 两个不同输入可能不会导致相同输出。即使两个输入键之间的变化很小也可能导致两个输出值完全不同。相同的输入值可能不会导致两个不同的输出。
- Pre-image resistance: 找不到$h^{-1}(value)$，除非"暴力求解"

#### 影响哈希函数表现的因素

- value是否均匀分布在表中

- 冲突解决方案

- 表的大小

这些因素影响哈希表查找插入和删除的复杂度

### 二、布隆过滤器 Bloom filter

#### 用途：

A Bloom filter is a space-efficient probabilistic data structure

检测一个元素是否在一个集合内

##### 例子

- 垃圾邮件过滤器：

  垃圾邮件可能说不是垃圾邮件

  不是垃圾邮件的邮件一定说不是垃圾邮件

- 拼写检查器

- 重复检查：帮助爬虫检查一个URL是否爬取过

#### 特点：

- False positive matches are possible：元素不在集合可能说在集合
- False negatives are impossible：元素不在集合一定说不在集合

#### 存数据x

设一个m位的0-1数组，并且初始化为0。设k个哈希函数，$h_1,h_2...,h_k$，值域为{1,2,...,m}

现要存一个数据x，则
$$
h_1(x)=1 \\
h_2(x)=1 \\
h_3(x)=1
$$

#### 查数据y是否在集合

如果$h_1(y)=1 , h_2(y)=1 , h_3(y)=1​$，就判断y在集合内，否则判断不在集合

#### 例子

<img src="E:\笔记\数据科学算法笔记\BloomFilter例子.PNG" width=60%>

<img src="E:\笔记\数据科学算法笔记\BloomFilter例子2.PNG" width=60%>

### 纳伪率（false positive）计算

$$
每个位不被哈希到的概率1-\frac{1}{m}\\
经历n个元素的存储，k个哈希函数还是0的概率，(1-\frac{1}{m})^{kn} \\
则经历上述过程还是1的概率为，1-(1-\frac{1}{m})^{kn} \\
那么根据Bloom filter的规则，纳伪的概率为\\
f = (1-(1-\frac{1}{m})^{kn})^k \\
当m充分大，有\\
f=(1-(1-\frac{1}{m})^{(-m)*(-kn/m)})^k=(1-(1-e^{-kn/m})^k
$$

##### 分析

- f随m(哈希表的大小)的增大而减小；
- f随n(添加元素的个数)的增大而增加

##### m，n不变时，选择一个k使f最小

$$
设p=e^{-kn/m},f=e^{kln(1-p)} \\
问题等价于 min_{arg\space p^*}(kln(1-p))\\
求导解得 p=\frac{1}{2}\\
k=\frac{m}{n}ln2
$$

### 局部敏感哈希 Locality-Sensitive Hashing

#### Jaccard距离&Jaccard相似度

$$
sim(A,B)=\dfrac{\lvert A\cap B\rvert}{\lvert A\cup B\rvert} \\
d(A,B)=1-\dfrac{\lvert A\cap B\rvert}{\lvert A\cup B\rvert}
$$

#### 目标 ： 寻找相似文档

##### 三部曲

- Shingling: 将文档转换为集合
- Min-Hashing: Convert large sets to short signatures, while preserving similarity
- Locality-Sensitive Hashing: Focus on pairs of signatures likely to be from similar documents

<img src="E:\笔记\数据科学算法笔记\hashing.png" width=60%>

##### Shingling

- 保存了上下文信息ordering of words
- A k-shingle (or k-gram) for a document is a sequence of k tokens（tokens可以是字母，单词……） that appears in the document
- 例子：tokens是字母

Let k = 2. String D =**abcab** can be converted into a set of 2-shlingles，S(D) = {ab，bc，ca}
Option: Shingles as a bag (multiset), count ab twice，S(D) = {ab; bc; ca; ab}:

- 再将这些k-shingle哈希映射为0-1向量，就可以用Jaccard相似度度量文档之间的相似度（交并集转换为按位的或与运算）

<img src="E:\笔记\数据科学算法笔记\shringling例子.PNG" width=60%>

##### Min-hashing

- 核心思想："hash" each column C to a small signature h(C),
  - h(C)足够小可以装入内存
  - $sim(C_1,C_2)$等于$h(C_1),h(C_2)$的相似度

- 目标：找到一个h使得
  - 如果sim(C1，C2) 很大，P(h(C1) = h(C2))也要很大
  - 如果sim(C1，C2)很小，P(h(C1) != h(C2))要很小

- Min-hashing

  - 定义$h_\pi(C)为向量C经过\pi打乱后第一个出现1的位置$（C是0-1向量）$\pi$为一个随意的排列

  $$
  h_\pi(C)=min_{\pi}\pi(C)
  $$

  

  - 例子

  <img src="E:\笔记\数据科学算法笔记\minhashing.PNG" width=60%>

  - min-hashing的性质

  $$
  P(h_\pi(C_1)=h_\pi(C_2))=sim(C_1,C_2)
  $$

  ​	证明

<img src="E:\笔记\数据科学算法笔记\minhashing证明.PNG" width=60%>

##### Locality-Sensitive Hashing