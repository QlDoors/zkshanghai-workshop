# 第5课 课后作业

## 第1题

$KZG$ 多项式承诺方案在 $Setup$ 阶段涉及到计算对秘密评估点 $\tau$ 的幂的承诺，这被称为“可信设置”，通常在被称为“Powers of Tau”的仪式中利用多方计算生成。 假如说有一天，你在一张纸条上找到了 $\tau$ 的值。 你怎么能用它来制作一个假的 $KZG$ 证明呢？

### 解：

$KZG$ 多项式承诺方案标准流程如下：

- $Setup(1^\lambda,d)\rightarrow srs=(ck,vk)$
  $$
  \begin{align} 
  ck&=\{[\tau^i]*1\}*{i=1}^{d-1} \\
   &=\{[\tau^0]G_1,[\tau^1]G_1\dots[\tau^{d-1}]G_1\} \\
  vk&=[\tau]G_2
  \end{align}
  $$
  
- $Commit(ck;f(X))\rightarrow C$
  $$
  \begin{align} 
  C&=f_0[\tau^0]_1+f_1[\tau^1]_1+\dots+f_{d-1}[\tau^{d-1}]_1 \\ 
  &=(\sum_{i=0}^{d-1}f_i[\tau^i])G_1  \\ 
  &=[f(\tau)]_1 \end{align}
  $$

- $Open(srs,C,z,y;f(X))\rightarrow \{0, 1\}$

  - $f(z)=y$
  - $Prove(ck,C,z,y;f(X)) \rightarrow \pi$
    - 构造商多项式 $q(\tau)=\frac{f(\tau)-y}{\tau-z}$
    - 发送证明 $\pi=Commit(ck;q(X))=[q(\tau)]_1$
  - $Verify(vk,C,z,y,\pi)\rightarrow \{0,1\}$
    - 验证 $e(C-[y]_1, [1]_2)\overset{?}{=}e(\pi, [\tau]_2-[z]_2)$

#### 伪造：

假如Prover需要证明点$(x_0,y_0)$属于多项式$p(X)$。

1. 构造一个多项式$p'(X) = g(X) + d(\tau - x_0) + [y_0-g(X)]h(X)$，这里$h'(\tau)=1$，$d \in \mathbb{F}$，$g(X)$为任一多项式

2. $Commit(ck;f(X))\rightarrow C = [p'(\tau)]_1 = [g(\tau) + d(\tau - x_0) + [y_0 - g(\tau)]h(\tau)]_1 = [d(\tau - x_0) + y_0]G_1$

3. 商多项式
   $$
   \begin{align}
   q(\tau) &= \frac {p'(\tau) - y_0} {\tau - x_0} \\
   &= \frac {d(\tau - x_0) + y_0 - y_0} {\tau - x_0} \\
   &= d
   \end{align}
   $$



## 第2题

从 $KZG$ 多项式承诺方案构造一个**向量承诺方案**。 （提示：对于向量 $m=\left(m_{1}, \ldots, m_{q}\right)$，是否存在一个“插值多项式”$I(X)$ 使得 $I\left(x_{i}\right)=y_{i}$？）

> **tip 有趣的事实**
>
> Verkle 树 [1] 是一种使用向量承诺而不是哈希函数的 Merkle 树。 使用 KZG 向量承诺方案，您能看出为什么 Verkle 树更高效吗？

### 解：

假设已知$n$个向量$\vec{m_0}, \vec{m_1}, \dots, \vec{m_{n-1}}$，构造一个拉格朗日插值多项式$I(X)$，使得$I(i)=\sum_{k=1}^q m_{ik}$，$m_{ik}$表示向量$\vec{m_i}$的第$k$维的值，有：
$$
\begin{align}
S_i = \sum_{k=1}^q m_{ik} \\
\tag{a} I(X) = \sum_{i=0}^{n-1} S_i \prod_{j=0, j \not= i}^{n-1} {\frac {X-j} {i - j}}
\end{align}
$$
下面对多项式$(a)$构造KZG承诺方案就可以了。



## 第3题

$KZG$ 多项式承诺方案对关系 $p(x)=$ $y$ 进行披露证明 $\pi$。 你能扩展这个方案来产生一个多重证明 $\pi$，让我们相信 $p\left(x_{i}\right)=y_{i}$ 对于点列表和评估 $\left(x_{i }, y_{i}\right)$ ？ （提示：假设您有一个插值多项式 $I(X)$ 使得 $I\left(x_{i}\right)=y_{i}$）。

### 解：

对于包含$k$个点的列表，我们对其构建拉格朗日多项式：
$$
I(X) = \sum_{i=0}^{k-1}y_i \prod_{j=0, j\not=i}^{k-1} {\frac {X-x_j} {x_i - x_j}}
$$
构造商多项式：
$$
\begin{align}
z(X) &= \prod_{i=0}^{k-1} (X - x_i) \\
q(X) &= \frac {p(X) - I(X)} {z(X)}
\end{align}
$$
因为$p(X) - I(X)$能被$z(X)$所有的线性因子整除，所以能被$z(X)$本身整除。

具体承诺方案如下：

1. $Commit(ck;p(X))\rightarrow C = [p(\tau)]_1 = [p(\tau)]]G_1$

2. $Open(srs,C,x_i,y_i;p(X))\rightarrow \{0, 1\}$

   - $Prove(ck,C,x_i,y_i;f(X)) \rightarrow \pi$
     - 构造商多项式 $q(X)=\frac{p(X)-I(X)} {z(X)}$
     - 发送证明 $\pi=Commit(ck;q(X))=[q(\tau)]_1$
   - $Verify(vk,C,x_i,y_i,\pi)\rightarrow \{0,1\}$
     - 验证 $e(C-[I(\tau)]_1, [1]_2)\overset{?}{=}e(\pi, [z(\tau)]_2)$

