# The-Gambler-s-Ruin-Problem
睡之前在推特看到 经典赌徒破产问题：A有1元，B有10元，每次都赌1元，两人每次输赢概率都是0.6.请问A赢光B钱的概率是多少？
发现可以有很多种解法。

# 定义转移矩阵

$$
\begin{bmatrix}
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
0 & 1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
0.35 & 0 & 0 & 0.65 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
0 & 0 & 0.35 & 0 & 0.65 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0.35 & 0 & 0.65 & 0 & 0 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & 0.35 & 0 & 0.65 & 0 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & 0 & 0.35 & 0 & 0.65 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & 0 & 0 & 0.35 & 0 & 0.65 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0.35 & 0 & 0.65 & 0 & 0 \\
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0.35 & 0 & 0.65 & 0 \\
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0.35 & 0 & 0.65 \\
0 & 0.65 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0.35 & 0 \\
\end{bmatrix}
$$

```
import numpy as np
trans = np.matrix([
    [1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
    [0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
    [0.35, 0, 0, 0.65, 0, 0, 0, 0, 0, 0, 0, 0],
    [0, 0, 0.35, 0, 0.65, 0, 0, 0, 0, 0, 0, 0],
    [0, 0, 0, 0.35, 0, 0.65, 0, 0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0.35, 0, 0.65, 0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0, 0.35, 0, 0.65, 0, 0, 0, 0],
    [0, 0, 0, 0, 0, 0, 0.35, 0, 0.65, 0, 0, 0],
    [0, 0, 0, 0, 0, 0, 0, 0.35, 0, 0.65, 0, 0],
    [0, 0, 0, 0, 0, 0, 0, 0, 0.35, 0, 0.65, 0],
    [0, 0, 0, 0, 0, 0, 0, 0, 0, 0.35, 0, 0.65],
    [0, 0.65, 0, 0, 0, 0, 0, 0, 0, 0, 0.35, 0]
])

```

# 吸收马尔科夫链

把转移矩阵写为下列形式:

$$
P = \begin{bmatrix} I & 0 \\
R & Q \end{bmatrix}
$$

I 是单位矩阵，表示吸收状态到自身的转移（概率为 1，相当于把吸收状态放到一起形成Identity矩阵）。

0 是零矩阵，表示吸收状态不会转移到非吸收状态。

R 表示非吸收状态到吸收状态的转移概率矩阵。

Q 表示非吸收状态之间的转移概率矩阵


把吸收状态行和列放一起


然后Q就是I矩阵区域的斜对角了，来提取它

```
Q = trans[2:,2:]
```

$$
Q = \begin{bmatrix}
0 & 0.65 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
0.35 & 0 & 0.65 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
0 & 0.35 & 0 & 0.65 & 0 & 0 & 0 & 0 & 0 & 0 \\
0 & 0 & 0.35 & 0 & 0.65 & 0 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0.35 & 0 & 0.65 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & 0.35 & 0 & 0.65 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & 0 & 0.35 & 0 & 0.65 & 0 & 0 \\
0 & 0 & 0 & 0 & 0 & 0 & 0.35 & 0 & 0.65 & 0 \\
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0.35 & 0 & 0.65 \\
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0.35 & 0 \\
\end{bmatrix}
$$



接着定义一个Identity matrix,和Q相同维度,然后计算 $F = (I-Q)^{-1}$

```
I = np.identity(10)
F = np.linalg.inv(I-Q)
```

**F矩阵特别重要，F矩阵中的 $F_{ij}$ 位置代表从位置i出发，在被吸收前（不管被哪头吸收）期望经过j状态的次数。 $F_{34}$ 代表从3元出发，在破产或赢光B之前，手上有4块钱的次数。**

还记得转移矩阵里定义的R矩阵吗？提取它！

```
R = trans[2:,0:2]
R.shape
```
**吸收矩阵**

```
F @ R
```

B矩阵：

$$
B = \begin{bmatrix}
0.53795 & 0.46205 \\
0.28916 & 0.71084 \\
0.15519 & 0.84481 \\
0.08305 & 0.91695 \\
0.04421 & 0.95579 \\
0.02330 & 0.97670 \\
0.01203 & 0.98797 \\
0.00597 & 0.99403 \\
0.00270 & 0.99730 \\
0.00095 & 0.99905 \\
\end{bmatrix}
$$

第一行第二列：0.46205正是我们要的答案，代表从1元出发，最终达到吸收态‘赢光B’的概率
