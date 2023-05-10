# 第3课 课后作业

给定整数 $x, m$，如果 $x$ 在模 $m$ 下是二次剩余，即存在整数 $s$，使得 $s^{2} \equiv x(\bmod m)$，则记作 $QR(m, x)=1$ ; 如果 $x$ 在模 $m$ 下不是二次剩余，则记作 $QR(m, x)=0$。


## 第1题 二次非剩余 Quadratic nonresidue

_假定_：证明者可以为所有 $m, x$ 计算 $QR(m, x)$

_公共输入_：正整数 $m, x$

_目标_：证明者想要说服验证者 $QR(m, x)=0$

::: tip 协议

1. Verifier 在与 $m$ 互质的元素中均匀地随机选取一个 $s \in \mathbb{Z}_{m}$，同时抛硬币 $b \gets_R \{0,1\}$。 设
    $$
    y \leftarrow \begin{cases}
        s^{2} x & \text { if } b=0, \\
        s^{2} & \text { if } b=1.
    \end{cases}
    $$
    Verifier 将 $y$ 发送给 Prover 并挑战 Prover 以确定 $b$。

2. Prover 计算 $QR(m, y)$ 并将其值发送回 Verifier

3. 如果 Verifier 从 Prover 收到的值确实等于 $b$，则 Verifier 接受； 否则它拒绝。

::::

您需要检查：

- (a) **完备性**：如果 $QR(m, x)=0$ 并且双方都按照协议行事，那么验证者总是接受。

- (b) **可靠性**：如果 $QR(m, x)=1$，那么无论 Prover 做什么（Prover 不必遵循协议），Verifer 都会以 $\geq 1 / 2$ 的概率拒绝

### 解：

### 1. 完备性

#### 1.1 if b == 0

$QR(m,y)=QR(m,s^2x)$

题设$QR(m,x)=0$等价于：找不到一个整数$r$使得$r^{2} \equiv x(\bmod m)$。

取一个满足$(a)$式的整数$a$
$$
\tag{a} a \equiv x(\bmod m)
$$
任取一整数$b$，有$(b)$式成立
$$
\tag{b} s^2 + bm\equiv s^2(\bmod m)
$$
$(a)式*(b)式$，可得：
$$
a(s^2 + bm) \equiv s^2x(\bmod m)
$$
##### 1.1.1 假设存在一整数$c$使$c=\sqrt{s^2+bm}$成立，则有

$$
\sqrt{a(s^2+bm)}=c\sqrt a
$$

由于$a$没有整数平方根，所以$c\sqrt a$肯定不是整数。此时： $QR(m,y)=QR(m,s^2x)=0=b $

##### 1.1.2 假设不存在整数c

$\sqrt{a(s^2+bm)}=\sqrt a *\sqrt{s^2+bm}$ 显然不是整数，此时： $QR(m,y)=QR(m,s^2x)=0=b $

综合1.1.1，1.1.2，可得：$QR(m,y)=QR(m,s^2x)=0=b $

#### 1.2 if b == 1

$QR(m,y)=QR(m,s^2)$

$s^2$至少有一个同余为自身，而$s^2$的平方根为$s$，所以：$QR(m,y)=QR(m,s^2)=1=b$

#### 结论：

综上，Prover返回b总是正确答案，Verifier总是会接受。

### 2. 可靠性

#### 2.1 Prover 不遵守协议

Prover 如果不遵守协议，对一个判定问题，他猜中的概率不会超过1/2。

#### 2.2 Prover遵守协议，且 b == 0

$QR(m,y)=QR(m,s^2x)$

题设$QR(m,x)=1$等价于：存在一个整数$r$使得
$$
\tag{c} r^{2} \equiv x(\bmod m)
$$


(c)式*(b)式，可得：
$$
r^2s^2 \equiv s^2x(\bmod m)
$$
$\sqrt{r^2s^2} = rs$，所以，$QR(m,y)=QR(m,s^2x)=1 \not= b$

#### 2.3 Prover遵守协议，且 b == 1

$QR(m,y)=QR(m,s^2)$

$s^2$至少有一个同余为自身，而$s^2$的平方根为$s$，所以：$QR(m,y)=QR(m,s^2)=1=b$

#### 结论

由2.2和2.3可知，如果Prover遵守协议，Verifier会有1/2的概率拒绝请求。结合2.3，可以得出：无论Prover做什么，Verifier都会以$\geq 1 / 2$ 的概率拒绝。



## 第2题 二次剩余 Quadratic residue

_公共输入_：正整数 $m, x$ 且满足 $\operatorname{gcd}(m, x)=1$

_私有输入_：证明者知道一个秘密整数 $s$ 使得 $s^{2} \equiv x \bmod m$

_目标_：证明者想要说服验证者 $QR(m, x)=1$ 而不透露有关 $s$ 的任何信息

（请注意，证明者可以通过向验证者发送 $s$ 轻松说服验证者 $QR(m, x)=1$，但这会完全暴露 $s$。）

::: tip 协议

1. Prover 在与 $m$ 互质的剩余中随机选择一个均匀的 $t \in \mathbb{Z}_{m}$。 Prover 将 $y \leftarrow x t^{2} \bmod m$ 发送给 Verifier。

1. Verifier 抛硬币 $b \gets_R \{0,1\}$ 并将 $b$ 发送给 Prover。

1. Prover 收到 $b$ 并将$u$值发送给 Verifier
    $$
    u \leftarrow \begin{cases}
        t & \text { if } b=0, \\
        s t & \text { if } b=1.
    \end{cases}
    $$

1. 验证者接受证明如果
    $$
    y \equiv \begin{cases}
        u^2 x \bmod m & \text { if } b=0, \\
        u^2 \bmod m & \text { if } b=1.
    \end{cases}
    $$
    否则拒绝。

::::

您需要检查：

- (a) **完备性**：如果双方都按照协议行事，那么 Verifier 总是接受。

- (b) **可靠性**：如果 $QR(m, x)=0$（特别是，Prover 不知道任何有效的 $s$ ），那么无论 Prover 做什么，Verifier 都以 $\geq 1 / 2$ 的几率拒绝。

- (b\*) **知识可靠性**：对于任何使得Verifier接受的证明算法，如果允许Verifier同时查询$b\in\{0,1\}$的两个值（对于 相同的 $t$ )，则 Verifier 可以从 Prover 中提取秘密 $s$。 （这里的重点是除非证明者“知道”$s$，否则证明者无法说服验证者。）

- (c) **零知识**：无论验证者做什么（验证者的行为可能与协议不同），验证者都可以在不与证明者交互的情况下自行模拟整个交互，这样如果验证者接受，那么记录下来的信息与实际交互几乎不可区分。

     提示：想象模拟器自己生成一个随机的 $u$ 而不是从 Prover 获取 $u$。

     （这部分有点棘手，因为允许对抗性验证器的行为与协议不同 - 请记住我们试图展示的是，无论验证器做什么，它都不会从证明者那里学到关于 $s$ 的信息 验证者不可能简单地自行生成。如果您想查看解决方案，请参阅 [Barak 笔记中的定理 13.6](https://files.boazbarak.org/crypto/lec_14_zero_knowledge.pdf) 的证明。）

### 解：

### 1. 完备性

#### 1.1 $\text { if } b=0$

$$
\begin{rcases}
y=xt^2 \bmod m \\
u=t \\
\end{rcases} 
\Rightarrow 
y=u^2x \bmod m 
\Rightarrow y \equiv u^2x\bmod m
$$

#### 1.2 $\text { if } b=1$

$$
\tag{a} u = st \Rightarrow u^2 \bmod m = s^2t^2 \bmod m
$$

$$
\tag{b}
\begin{rcases}
s^{2} \equiv x \bmod m \\
\gcd(m, x)=1
\end{rcases} \Rightarrow \exist 整数a使得s^2 = x + am
$$

将$(b)$代入$(a)$，可得：
$$
\begin{align}
u^2 \bmod m &= s^2t^2 \bmod m \\
&= (x+am)^2t^2 \bmod m \\
&= xt^2 \bmod m + (amt)^2 \bmod m \\
&= xt^2 \bmod m \\
&=y \\
&\Rightarrow y \equiv u^2 \bmod m
\end{align}
$$
综合 1.1，1.2 完备性得证。

### 2. 可靠性



## 第3题 双线性自映射意味着DDH的失效 Self-pairing implies failure of DDH

设 $\mathbb{G}$ 和 $\mathbb{G}_{T}$ 是相同素数阶 $q$ 的阿贝尔群（以加法写出）。 设 $g \in \mathbb{G}$ 为生成元。 假设我们有一个可有效计算的非退化双线性对

$$
e: \mathbb{G} \times \mathbb{G} \rightarrow \mathbb{G}_{T}
$$

给出一个有效的决策算法，给定 $\alpha g, \beta g, y \in \mathbb{G}$（其中 $\alpha, \beta \in \mathbb{Z}_{q}$为未知)，判断是否$\alpha \beta g=y$。

提示：计算与 $g$ 的映射。

此练习表明确定性 Diffie-Hellman (DDH) 假设对于双线性自映射是错误的。



## 第4题 BLS 签名聚合 BLS signature aggregation

::: tip 设置

1. 映射 Pairing $e: \mathbb{G}_{0} \times \mathbb{G}_{1} \rightarrow \mathbb{G}_{T}$，其中$\mathbb{G}_{0} , \mathbb{G}_{1}, \mathbb{G}_{T}$ 是素数阶 $p$ 的阿贝尔群（以加法写出），生成元分别是 $g_{0}, g_{1}, g_{ T}$

1. 哈希函数 $H$ 在 $\mathbb{G}_{1}$ 中取值
::::

BLS签名方案定义为：

- $\operatorname{KeyGen()}$: 选择随机（私钥）$sk=\alpha \leftarrow{ }_{R} \mathbb{Z}_{q}$ 并设置（公钥）$pk \leftarrow \alpha g_{0} \in \mathbb{G}_{0}$
- $\operatorname{Sign}(sk, m)$: 输出 $\sigma \leftarrow \alpha H(m) \in \mathbb{G}_{1}$ 作为消息 $m$ 上的签名
- $\operatorname{Verify}(pk, m, \sigma)$ ：如果 $e\left(g_{0}, \sigma\right)=e(pk, H(m))$，则接受。

检查验证算法是否始终能接受正确签名。 还争辩说，如果给伪造者的是 $pk$ 而不是 $sk$，那么为任何消息 $m$（伪造者可以自由选择 $m$ ）想出一个伪造的签名 $\sigma$ 是计算上不可行的。 这在选择的消息攻击下被称为存在不可伪造。 你能指定一些计算难度假设吗？

请检查验证算法是否总是接受正确的签名。并论证：在给定$pk$但没有$sk$的情况下，（伪造者可以选择消息）对于任何消息$m$，构造出一个伪造的签名$\sigma$是计算上不可行的 。这被称为 _在选择消息攻击下的存在性不可伪造性_ 。您能找到一些计算难度假设吗？

**签名聚合**。 给定三元组 $\left(pk_{i}, m_{i}, \sigma_{i}\right)$ 其中 $i=1, \ldots, n$（即来自 $n$ 个用户），我们可以聚合这些 $n$ 个签名 $\sigma_{1}, \ldots, \sigma_{n}$ 通过简单地在 $\mathbb{G}_{1}$ 中求和，将其合并为一个签名：

$$
\sigma \leftarrow \sigma_{1}+\cdots \sigma_{n} \in \mathbb{G}_{1}
$$

给定 $\left(pk_{1}, m_{1}\right), \ldots,\left(pk_{n}, m_{n}\right)$ 和聚合签名 $\sigma$，表明我们可以 通过检查来验证确实所有 $n$ 用户都签署了他们的消息

$$
e\left(g_{0}, \sigma\right)=e\left(pk_{1}, H\left(m_{1}\right)\right)+\cdots+e\left(pk_{n}, H\left(m_{n}\right)\right)
$$

注意：由于 _恶意公钥攻击_ ，上述签名聚合方案不安全。 使方案安全的一种方法是确保所有消息 $m_{i}$ 是不同的，例如，要求每个 $m_{i}$ 以用户的公钥 $pk_{i}$ 开头。

有关 BLS 签名聚合的更多信息，请参阅 <https://crypto.stanford.edu/~dabo/pubs/papers/BLSmultisig.html>
