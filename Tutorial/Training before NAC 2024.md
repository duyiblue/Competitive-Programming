## Tic Tac Toe Counting

来源：NAC 2022 https://qoj.ac/contest/943/problem/4234

备注：这场最简单的题。vp 时队友跟我讲了思路。我写的。

做法：

最多一共只有 $3^9 = 19683$ 种局面。这些局面组成了一个 DAG。建图后拓扑排序即可预处理出所有答案。

建图的方法是，爆搜出所有游戏进程。最多只有 $9! = 362880$ 种进程。

代码：https://qoj.ac/submission/405504

## Word Ladder

来源：NAC 2022 https://qoj.ac/contest/943/problem/4237

备注：vp 时通过人数很多。我一直在死磕这道题，但没想出好做法。最后是爆搜，然后打表通过的。

做法：

首先有一个观察：如果有一个合法的 Word Ladder，那么它里面任意连续的一段也是一个合法的 Word Ladder。也就是说，我们只需要构造出来一个足够长的答案，就能应付所有的 $n$ 了。

容易想到如下的构造方法，是满足“每次只改变一个字母”和“没有捷径”的条件的：

```
aaaaaaaaaa
aaaaaaaaab
aaaaaaaabb
aaaaaaabbb
...
bbbbbbbbbb
bbbbbbbbbc
bbbbbbbbcc
...
cccccccccc
...
zzzzzzzzzz
```

但是，这样只能构造出 251 个词。

考虑把 10 个字母分为两组，每组 5 个。第一组先保持为 ``aaaaa`` 不动，第二组按上述方法从 ``aaaaa`` 走到 ``zzzzz``，这样有 126 个词（称为第 1 段）。然后动一下第一组，改成 ``aaaab``，第二组再重复上述过程，就又有了 126 个词（称为第 2 段，以此类推）。每段内部，保持第一组的 5 个字母不变，第二组可以改 125 次。

但是这个方法有问题。两段之间会存在捷径。比如第 1 段的 ``aaaaa aaaab`` 可以直接跳到第 2 段的 ``aaaab aaaab``。因为第 1 段和第 2 段的前五个字母，只差了一位。

解决这个问题的方法是，两段之间，让第一组走两步。例如：

```
...
aaaaa zzzzz // 第 1 段结束
aaaab zzzzz // 第一组走一步
aaabb zzzzz // 第一组再走一步（这是解决问题的关键！）
aaabb zzzza // 开始第 2 段
aaabb zzzaa
...
```

这样能构造出 $\frac{126}{2} = 63$ 段，每段大约 $126$ 个词，共有 $63\times 126 > 5000$ 个词，满足要求。

代码：https://qoj.ac/submission/405505

## Jumbled Stacks

来源：CERC 2023 https://codeforces.com/gym/104871/problem/J

备注：vp 时队友和我讲了思路，我写的。

模拟。思路很简单，就是选择排序，逐个位子确定，每次选择后面最小的放过去。

放的时候，要先把最小的卡所在的 stack 里，它上面的东西，全部弹出。然后把目标 stack 里，目标位置上面的东西全部弹出；如果外面空位不够了，就把最小的卡暂时移到另一个已满的 stack 的顶端。

操作次数上限 $10^5$ 足够大，不需要担心。

代码实现中细节比较多。

代码：https://codeforces.com/gym/104871/submission/260458381

## Drying Laundry

来源：CERC 2023 https://codeforces.com/gym/104871/problem/D

备注：vp 时做出。

因为 $1\leq L_j\leq 3\cdot 10^5$，所以我们可以在询问前先把所有答案预处理出来。

首先假设所有床单都用 slow 的方法（只占一根绳子）晾。这样所需的绳子长度是最短的。考虑求这个最短长度。

考虑二分 $L$。

如何判断一个绳子长度 $L$ 是否合法呢？我们对所有床单的长度做一个 01 背包：$c_i$ 是一个为 0 或 1 的值；$c_i = 1$ 当且仅当存在一个床单的集合 $S$ 里面的床单长度之和为 $i$。设 $p_i = \max_{j = 0}^i \{j \mid c_j = 1\}$。设 $s = \sum_{i = 1}^{N} d_i$。那么 $L$ 合法当且仅当 $s - p_L \leq L$。也就是在一根绳子上挂了尽可能多的床单（$p_L$）后，剩下的全挂在另一根绳子上。

然后为了使得晾晒时间更短，我们可以把一部分床单变成 fast 的方法。因为所花的时间只由最大的 $t$ 决定，所以如果不把 $t_i^{\text{slow}}$ 最大的换成 fast，换其他的都是没有用的。因此，我们可以把床单按 $t_i^{\text{slow}}$ 从大到小排序，每次把最大的换成 fast。这样相当于，对剩下的（仍然 slow 的）床单，用上述方法做二分和 01 背包，再把得到的长度加上 fast 这些床单的长度就好了。

每次把一个 slow 变成 fast，相当于从原来的背包里移除一样东西。移除不好实现，不妨倒过来做。初始时假设所有床单都是 fast，每次选一个变成 slow。这样就变成了往背包里加入一个东西。可以用 bitset 来实现这个背包。此外，二分 $L$ 也是不需要的。因为每次多一个床单从 fast 变成 slow，所需的长度一定单调下降。用类似 two-pointers 的 while 循环更新答案就可以了。

时间复杂度 $\mathcal{O}(\frac{NL}{\omega} + Q)$。

代码：https://codeforces.com/gym/104871/submission/260463990

注：在 bitset 里找某一位之前的第一个 1（$p_i$），C++ 自带的 bitset 好像没有这个功能。所以我代码里是用一个 for 循环暴力实现的。虽然 AC 了，但时间复杂度其实不对。按理说应该手写 bitset。

## ICPC Camp

来源：NAC 2020 https://qoj.ac/contest/474/problem/3167

备注：vp 时做出。

先把经典题和创意题都按难度从小到大排序。

二分 $D​$。确定了 $D​$ 后，每道经典题（假设难度为 $x​$）能匹配的创意题就是一个区间。具体来说，是区间 $[x - D, x + D]​$ 和前缀 $[0, s - x]​$ 的交。

通过如下贪心策略可以使匹配数量最大化：按难度**从大到小**考虑所有经典题。每次取它能匹配的区间里，难度**最大**的，尚未被匹配的创意题。

正确性：因为从大到小考虑经典题，所以每道题对应的区间**左**端点是单调递减的。（具体来说，$[x - D, x + D]$ 单调递减，$[0, s - x]$ 单调递增。它们的交的右端点不一定单调递减，但左端点一定单调递减。）假设两道经典题：$x_1 > x_2$。$x_1$ 区间里有两道尚未被匹配的创意题：$y_1 < y_2$。假设，在某一个最优解里，$x_1$ 匹配了 $y_1$，$x_2$ 匹配了 $y_2$（没按我们的贪心策略）。那么，让 $x_1$ 匹配 $y_2$，$x_2$ 匹配 $y_1$ 一定也是一个合法的解，理由如下：$x_1$ 能匹配 $y_2$ 是显然的，因为我们已经说了 $y_1$, $y_2$ 都在它的区间里。$x_2$ 为什么一定能匹配 $y_1$，就是因为左端点单调递减。如果 $x_1$（左端点更大）能匹配 $y_1$，则 $x_2$ 一定也能匹配 $y_1$。

用线段树上二分实现这个贪心即可。

时间复杂度 $\mathcal{O}(n\log n\log D)$。

代码：https://qoj.ac/submission/409440

## Editing Explosion

来源：NAC 2020 https://qoj.ac/contest/474/problem/3169

考虑如果知道了两个串 $s​$, $t​$，如何求他们的 Levenshtein Distance。可以用一个和求 lcs 类似的 dp 来求：设 $\mathrm{dp}(i, j)​$ 表示 $s​$ 前 $i​$ 位和 $t​$ 前 $j​$ 位（这两个子串）的 Levenshtein Distance。则：
$$
\mathrm{dp}(i, j) = \min\{ \mathrm{dp}(i - 1, j) + 1, \mathrm{dp}(i, j - 1) + 1, \mathrm{dp}(i - 1, j - 1) + [s_i \neq t_j]\}
$$
三种转移分别代表了对 $t$ 插入一个字母、删除一个字母、和修改一个字母。

回到原问题。我们不知道 $t$ 串，如何求其数量？

观察上述 dp。它的每一列（固定 $j​$ 不变），按 $i​$ 从小到大（从 $0​$ 到 $|s|​$）都是一个数组。这个数组有如下性质：

- 长度为 $|s| + 1$。因为 $|s|\leq 10$，所以这个数组长度很短。
- 它是单调不下降的。
- 它的数值不超过 $11​$（因为 $d\leq 10​$。所以所有超过 $d​$ 的数可以一律视为 $11​$。）

符合这三个性质的，不同的数组，只有 ${22\choose 11} \approx 7\times 10^5$ 个（插板原理），记这个数量为 $m$。

把这 $m$ 个数组编号。然后作为 dp 的一维。具体来说，设 $f(j, v)$ 表示，有多少个长度为 $j$ 的字符串 $t$，满足 $\mathrm{dp}(\dots, j)$ 这一列为数组 $v$。从 $j$ 向 $j+1$ 转移时，枚举 $t$ 的第 $j+1$ 个字符是什么。用这个字符（套用原来的 dp）求出新的状态 $v'$。然后 $f(j+1, v')\texttt{+=} f(j, v)$。

$t$ 的长度最多不超过 $|s|+d\leq 20$。

代码实现时，甚至不需要把 $m$ 个数组编号，直接用 ``map<vector<int>, int> f[21]`` 即可。

时间复杂度：$\mathcal{O}(|t| \cdot |\Sigma|\cdot |s|\cdot m\log m)$。其中 $|t| = |s| + d\leq 20$，$|\Sigma|$ 为字符集大小，即 $26$。看似很大，但其实状态数量（$m$）达不到理论上限，所以能通过。

代码：https://qoj.ac/submission/410391

## Lunchtime Name Recall（未 AC）

来源：NAC 2020 https://qoj.ac/contest/474/problem/3170

备注：大概理解了做法。目前还在 TLE 中。等有时间了再来重新实现一下。

问题可以抽象为，让你构造一个 $m$ 行 $n$ 列的 01 矩阵，每行里 1 的数量是给定的，使得 unique 的列数尽可能多。

我们把相同的列视为一组。则 $n$ 个列会形成若干个组。所有组的大小之和是 $n$。把这些组的大小列出来，排好序，会形成一个数列。爆搜发现，这样的数列并不多。具体来说，我们要数的是单调不下降的正整数序列，长度任意，所有元素之和为 $n$。记这样的序列数量为 $t(n)$。发现 $t(30) = 5604$。

假设考虑了前若干行。有一个组，目前大小为 $x$（也就是有 $x$ 个相同的列）。那么在下一行里，我可以使用 $y$ 个 1（$1\leq y < x$），把这组拆成大小分别为 $y$ 和 $x-y$ 的两组。也就是说，在每一行里，我们都可以用 1 来切分一些组。最终目标是让大小为 $1$ 的组的数量尽可能多。

设 $s_1$, $s_2$ 为两个单调不下降的正整数序列，且两个序列里所有元素之和为 $n$。设 dp 状态 $(i, j, s1, s2)$ 表示考虑了前 $i$ 行，第 $i$ 行还剩 $j$ 个 1 没有用，目前已经切分出去的组是 $s_2$ 里面这些，还没切过的组是 $s_1$ 里面这些。$\mathrm{dp}(i, j, s_1, s_2)$ 是一个 bool 值，表示这个状态是否有可能。初始时，$\mathrm{dp}(1, a_1, \{n\}, \{\}) = 1$，其他位置都是 $0$。转移就是每次从 $s_1$ 里选一个组，一切为二（切的大小也要枚举），加入到 $s_2$ 中。即 $\mathrm{dp}(i, j, s_1, s_2)\to \mathrm{dp}(i, j - y, s_1 \setminus \{x\}, s_2 \cup\{y, x - y\})$。

我目前还在 TLE 中。网友可以思考一下如何实现并分析一下复杂度 QAQ。

## Skills in Pills

来源：CERC 2022 https://qoj.ac/contest/1070/problem/5249

把题面里的 $i$, $j$ 称为 $a, b$。

假设允许 A 和 B 在同一天吃，那么最优方案显然就是在第 $a, 2a, 3a, \dots$ 天吃 A，在第 $b, 2b, 3b, \dots$ 天吃 B。

回到原问题。

设 $l = \mathrm{lcm}(a, b)$。那么在第 $l$ 天我们会第一次遇到同一天吃了两种药的情况。为解决这个冲突，且尽可能少吃药，容易想到把某种药移到第 $l - 1$ 天（因为 $a, b\geq 2$，所以第 $l - 1$ 天一定是空着的）。具体移哪个，我们还不知道。但我们知道，如果发生冲突，只会把某种药往前移一天，移更多肯定不优（一个比较显然的贪心，我还没证明）。

所以可以设 $\mathrm{dp}(i, t)$，表示后面剩余 $i$ 天，此前的两天是 AB（$t=0$）还是 BA（$t = 1$），在这后 $i$ 天最少需要吃多少药。用记忆化搜索来实现这个 dp。

以此前两天是 AB 为例。我们要找到下一次冲突的位置，相当于解这个方程：$ax = by + 1$（其中 $x, y$ 是未知数，我们要求其整数解，使得 $ax > 0$ 且 $ax$ 最小）。可以用扩展欧几里得求解。具体来说，如果 $\mathrm{gcd}(a, b)\neq 1$，则这个方程无整数解（裴蜀定理）。否则，可以用扩展欧几里得求出一组可行解 $x_0, y_0$。那么通解就是 $x = x_0 + bk$, $y = y_0 - ak$（$k$ 为任意整数）。所以使得 $ax>0$ 且 $ax$ 最小的解就是 $x = (x_0 \% b + b) \% b$。

求出下一次冲突的位置后，要么把 A 往前移一格，要么把 B 往前移动一格（解决这个冲突），然后继续递归即可。

时间复杂度 $\mathcal{O}(n)$。

代码：https://qoj.ac/submission/412007

## Dot Product

Source：EC Final 2023 https://qoj.ac/contest/1522/problem/8052?v=1

Note：My teammate told me the most important observation. I did the rest.

Observation: A final permutation $a$ is valid if and only if it can be obtained from the permutation $1, \dots, n$ by selecting several non-overlapping adjacent pairs, and swap each of the selected pairs.

Another important fact: The cost to sort a permutation by swapping adjacent elements is the number of inversions in that permutation.

We consider each position in the final permutation from left to right, keeping in mind that the final permutation needs to satisfy our observation. Let $\mathrm{dp}(i)$ be the minimum cost (i.e., inversions) generated by the first $i$ places in the final permutation.

Let $w(i)​$ be the number of **values** smaller than $i​$ whose position in the **original** permutation is right to the position of value $i​$. Formally, $w(i) = \sum_{j = 1}^{i - 1} [\mathrm{pos(j) > \mathrm{pos}(i)}]​$. This is where inversions emerge.

Then $\mathrm{dp}(i) =\min\{dp(i - 1) + w(i), \mathrm{dp}(i - 2) + w(i) + w(i - 1) - [\mathrm{pos(i - 1) > \mathrm{pos}(i)}]\}$. This is mainly due to our initial observation: we either keep position $i$ unmoved, or swap it with position $i - 1$; we cannot perform further change beyond that. Note that we consider the final permutation to be $1, \dots, n$ by default; that is, if we don't swap, then the value on position $i$ will be $i$. If we swap position $i$ with $i - 1$, their relationship relative to other elements will not change, so the only thing affecting the number of inversions is the positions of $i$ and $i - 1$ relative to each other in the original permutation, reflected by $[\mathrm{pos(i - 1) > \mathrm{pos}(i)}]$ in the dp formula.

The time complexity is $\mathrm{O}(n\log n)$.

Code: https://qoj.ac/submission/412147

## Travel 2

Source: EC Final 2023 https://qoj.ac/contest/1522/problem/8056?v=1

Note: I spent a lot of time on this problem when vping, but finally worked it out.

Consider a dfs tree of the original graph. Initially, because we don't know anything about any node, we simply walk to the first edge of the current node. For every node that we visit for the first time, we consider it a child of the node where we were coming from.

When dfsing, we may encounter a issue that we jumped back to an ancestor of the current node. Usually, we would simply ignore that ancestor and get back to the "current node," continuing to dfs. However, we might not know the index of the edge from the ancestor to our "current node." Of course, one way to solve this issue is to go down the path on the dfs tree from the ancestor to the "current node"; we do know the indices of these edges (because we have used them), but that would take us too many operations.

Thus, we give up the idea of getting back to the "current node." Instead, we would continue exploring from the ancestor, using its unvisited edges, with the hope that someday we will eventually get back to the node where we left.

More generally, whenever we get to a node, we check whether all its edges have already been visited. If not, we try the first unvisited edge; otherwise, we get back to its father. (Every node knows its father; it's just the node where we were coming from when first visiting it.) This journey ends when we finally get back to the root and find all its edges visited.

However, we might mistakenly end the journey without visiting all the edges. Specifically, this happens when the edge from a node to its father is not the last edge (index-wise) from that node. To avoid this, we do a check when traveling from a child to its father: if not all edges of that child are used, we get back to the child immediately and continuing exploring the child's edges; we only get back to a node's father when all edges from the child have been visited.

The cost is exactly $2m + 2n$, where $2m$ is the cost to use every edge from both directions, and $2n$ is the cost when we need to get back to a node from its father (in the case that we mistakenly get to its father when the child still has some edges unused) or get to a node's father (when all edges from the child have been used).

Note that we don't actually need to do dfs. Just a while loop is perfect.

Code: https://qoj.ac/submission/412168

## Best Carry Player 4

Source: EC Final 2023 https://qoj.ac/contest/1522/problem/8057?v=1

Note: My teammate ACed this problem during vp. I learned this solution from him.

Firstly, it is easy to determine whether the answer is zero. Special judge this case.

Otherwise, we want to create as many pairs with sum $\geq m - 1$ as possible, and at least one pair with sum $\geq m$.

We first ignore the second requirement, just focusing on generating as many pairs with sum $\geq m - 1$ as possible. We can use greedy to create these pairs. Consider the the values of digits of $A$ from largest to smallest. Let's say the current value is $i$, and you have $a_i$ $i$s. The value in $B$ to pair with $i$ is at least $j = m - 1 - i$. We might also use $j + 1$, $j +2$, etc., if $a_i > b_j$ (i.e., $b_j$ is used up). Because we iterate over $i$ from largest to smallest, trying the smallest possible $j$ (i.e., $j = m - 1 - i$) and using it up generates the best answer we can get by greedy.

Because in the above greedy process, we sometimes move to $j + 1$, $j + 2$, etc., we might have already created a pair with sum $\geq m$. In this case, the result of the greedy is just the answer. Otherwise, every pair generated by the greedy has a sum equal to $m - 1$. We can break up one pair and create a new pair with sum $\geq m$ either by using some unused $b$ or breaking up another pair and snatch its $b$ (in which case the answer will decrease by $1$).

Time complexity: $\mathcal{O}(m)​$.

Code: https://qoj.ac/submission/412263

## Binary vs Ternary

Source: EC Final 2023 https://qoj.ac/contest/1522/problem/8058?v=1

Note: My teammate ACed this problem during vp. I learned this solution from him.

Firstly, there's a special case: "$1$" cannot become any other string, nor can any string become it. Besides that, all other strings can be achieved.

Here're some basic building blocks that we can use to convert the ternary to the binary. The three columbis from left to right are decimal, ternary, and binary numbers respectively.

```
0 0 0 // also 00->0, 000->0, etc.
1 1 1
3 10 11
4 11 100
```

Using $10\to 11​$, we can convert all $0​$s in $A​$ to $1​$.

Using $11\to 100 \to 110 \to 111$, we can add as many $1$s to $A$ as we'd like.

Using $111\to 1100 \to 10000 \to 10 \to 11$, we reduce as many $1$s as we'd like.

Using $11 \to 100 \to 10$, we can insert a $0$ at any place.

## Trails (Hard)

Source: https://codeforces.com/contest/1970/problem/E3

Note: I solved only the medium version ($\mathcal{O}(m^3\log n)$) of this problem during vp.

The official tutorial is very clear. I understand it now and will write the code when I have more time. https://codeforces.com/blog/entry/129149

## Swapping Seats

Source: CCC 2020 S4 / NAPC Problem T https://vjudge.net/contest/625365#problem/T

Without loss of generality, we assume the final sequence is something like ``AA...BB...CC...AA``. Formally, it is $x$ ``A``s followed by $y$ ``B``s, followed by $z$ ``C``s, followed by $w$ ``A``s, where $x + y + z + w = n$, and $x + w$, $y$, $z$ equal to the number of ``A``s, ``B``s, and ``C``s in the original string respectively. $y$ and $z$ are fixed; $w$ depends solely on $x$. So, we enumerate $x​$ to find the best possible answer.

The minimum number of operations needed to sort an array by swapping is $\text{the length of the array} - \text{the number of circles}​$. Here we assume all elements in the array are distinct, so it is like a permutation; this is where we get the idea of "circles." Note the difference between this conclusion and the one for the case where we can only swap *adjacent* elements, in which case the minimum number of operations would be the number of inversions.

To minimize the number of operations, we need to maximize the number of circles. Therefore, we want to make each circle as small as possible.

- Of course, the best thing for us is a circle of size $1$, which means a character is at exactly the place where it needs to be, no need for moving at all. More specifically, the ``A``s in the original string on positions $[1, x]$ and $[x + y + z + 1, n]$ don't need to move and can form a circle of size $1$ with itself, so are the ``B``s on positions $[x + 1, x + y]$ and the ``C``s on positions $[x + y + 1, x + y + z]$.
- The second best choice is to form circles of size $2$. This means we pair a ``B`` on ``A``'s position (i.e., in the range $[1, x]$ or $[x + y + z + 1, n]$) and an ``A`` on ``B``'s position (i.e., in the range $[x + 1, x + y]$); or, similarly, a ``C`` and an ``A``, or a ``C`` and a ``B``, on each other's position.
- After exhausting the previous two types, all that left are circles of size $3​$: e.g., an ``A`` that needs to move to ``B``'s positions, a ``B`` that needs to move to ``C``'s positions, and a ``C`` that needs to move to ``A``'s positions.

As said, we enumerate $x$. Then the length of the four segments are known. Using a prefix sum, we also know the numbers of ``A``s, ``B``s, and ``C``s on each segment. We basically have $12$ integers on our hand, representing the number of each character on each segment. Using them, we can calculate the maximum number of circles in $\mathcal{O}(1)$. So, the total time complexity is $\mathcal{O}(n)$.

Code: https://vjudge.net/solution/51320778/AQI7TRT0KCbsPcImp32y

## Random Permutation

Source: EC Final 2023 https://qoj.ac/contest/1522/problem/8050?v=1

For a fixed number $x$ and a sequence $a$ of length $n$, there's an algorithm to find the number of intervals whose median is $x$, in $\mathcal{O}(n)$ time. The algorithm is as follows: Construct a sequence $c$:
$$
c_i = \begin{cases}
-1 && a_i < x\\
0 && a_i = x\\
1 && a_i > x
\end{cases}
$$
Let $s$ be the prefix sum of sequence $c$ (i.e., $s_i = \sum_{j = 1}^i c_i$). Then the median of an interval $[l, r]$ is $x$ iff $s_r - s_{l - 1} = 0$ or $1$. We can find the number of such intervals in linear time.

Back to the original problem.

Because the permutation is random, when an interval is large, its median will be close to $\frac{n}{2}$. Therefore, if a number $x$ is *not* close to $\frac{n}{2}$, the intervals where $x$ is the median would be small.

For every number $x$, let $[L_x, R_x]$ be the maximum possible range in which $x$ is could be a median. Formally:
$$
L_x = \min\{l \mid \exist r: \mathrm{median}(p_l, \dots, p_r) = x\}\\
R_x = \max\{r \mid \exist l: \mathrm{median}(p_l, \dots, p_r) = x\}
$$
We know that when $x$ is not close to $\frac{n}{2}$, $[L_x, R_x]$ is small. Therefore, $\sum_{x = 1}^n (R_x - L_x)$ can't be large; in fact, it is $\mathcal{O}(n^{1.5})$. Thus, if we know $L_x$ and $R_x$ for all $x$, we can brute-forcely scan the entire range, applying the linear algorithm introduced at the beginning.

Hence, our task becomes to find $L_x$ and $R_x$.

Enumerate $x$ from smallest to largest. Maintain the sequence $s$ (the prefix sum of $c$). We can write the definition of $L_x$ and $R_x$ more precisely:
$$
L_x = \min\{l \mid \exist r\geq \mathrm{pos}(x): s_r - s_{l - 1} = 0 \text{ or } 1\}\\
R_x = \max\{r \mid \exist l\leq \mathrm{pos}(x): s_r - s_{l - 1} = 0 \text{ or } 1\}
$$
Let's first try to find $L_x$. $\exist r\geq \mathrm{pos}(x): s_r - s_{l - 1} = 0$ is equivalent to the statement that the value $s_{l - 1}$ exists in $s_{\mathrm{pos}(x)}, \dots, s_n$ (and $=1$ is similar). Thus, we want to first find all $s_r$s for $r\geq\mathrm{pos}(x)$. Because values in $s$ only increase or decrease by $1$ at a time, all $s_r$s ($r\geq \mathrm{pos}(x)$) must cover a consecutive range of values. In other words, if we find the maximum value $u = \max\{s_r \mid r\geq \mathrm{pos}(x)\}$ and the minimum value $d = \min\{s_r \mid r\geq \mathrm{pos}(x)\}$, then $\{s_r \mid r\geq \mathrm{pos}(x)\} = [d, u]$. Using a segment tree to maintain the range-maximum/minimum values of $s$, we can obtain $d$ and $u$ with a simple query.

Then, the requirement that $\exist r\geq \mathrm{pos}(x): s_r - s_{l - 1} = 0 \text{ or } 1​$ is translated to "$s_{l - 1}​$ is in the range $[d - 1, u]​$". (Here we use $d-1​$ because $s_r - s_{l - 1}​$ can be $1​$.)

We need to find the maximum $l$ such that for all $i < l$, $s_{i - 1}$ is *not* in the range $[d - 1, u]$. Using the same segment tree that maintains the range-maximum/minimum values of $s$, we can find such $l$ with a binary search on the segment tree (线段树上二分). This $l$ is just $L_x$.

We can find $R_x$ in similar ways: first, get the value range of $\{s_l\mid l\leq \mathrm{pos}(x)\}$: $[d_2, u_2]$. Then find the minimum $r$ such that for all $i > r$, $s_i$ is not in $[d_2, u_2 + 1]$.

Hence, we can find all $L_x$s and $R_x$s in $\mathcal{O}(n\log n)$.

The entire solution works in $\mathcal{O}(n\log n + n^{1.5})$.

Code: https://qoj.ac/submission/413351

## Monotony

Source: https://qoj.ac/contest/144/problem/3005

Note: solved during vp.

Enumerate the set of columns that we choose.

Then we know which rows are possible by looking at their values on the chosen columns. (check for horizontal monotonicity)

Then we need to focus only on the relationship between rows (vertical monotonicity). We can do a DP to select the rows. $\mathrm{dp}(i, j)$ means that the last two chosen rows are $i$ and $j$ ($i > j$). By comparing the rows $i$ and $j$, we know whether each column is increasing or decreasing, so we know whether we can add a new row $k$ ($k > i$), making the DP transition.

Time complexity: $\mathcal{O}(2^nn^3)$.

## Cutting Strings

Source: https://qoj.ac/contest/144/problem/3009

Note: solved during vp.

Greedy. First look at the letter $\texttt{z}$. If $k$ is large enough, we keep all the $\texttt{z}$s at the beginning: that is, we delete everything before the last $\texttt{z}$ that is not $\texttt{z}$.

For example: ``[...]z[...]zzz[...]zz[...]z[...]`` (letters other than $\texttt{z}$ are omitted). Here, we use four operations to delete everything in the first four brackets, gathering all $\texttt{z}$s at the beginning.

Then we recursively deal with the last bracket, doing the same thing for $\texttt{y}$, $\texttt{x}$, .... Until for some letter $c$, we don't have enough number of operations left to gather them all to the front.

Now the sequence will be something like: ``zzzyyxxx[...]c[...]cc[...]ccc[...]`` (all letters omitted are smaller than $c$). Here we need to be careful. For example, if we have only $1$ operation left, instead of removing the part before the first $c$, we can actually remove everything before the 3 consecutive $c$s (the last block of $c$), making the resulting string alphabetically larger.

So, if the number of operations left is not sufficient to move all occurrences of the current letter ($c$) to the front, we need to do something tricky. Note that this won't happen for every letter: just the last letter, and then we'll use up all $k$ operations and stop.

By greedy, we want to first maximize the number of $c$s that we gather at the front. Secondly, we want to alphabetically maximize the string beind the last $c$ (This is just a suffix of the original string, unchanged. This is because if we spend any operation to change that suffix, it would be better to save that operation for moving more $c$s to the front.)

Consider how to do each of the two parts. Part 1: Let's say we have $k'$ operations left. We enumberate which $c$ will be the last one of the consecutive $c$s gathered at the front. Before this $c$, we have several blocks of $c$s. By using $k'$ operations, we can keep $k'$ blocks, so we select the largest $k'$ blocks among them. Thus, we need a data structure that supports inserting a number and querying the sum of the $k'$ largest numbers. A priority queue would be great for this.

Part 2: to compare two suffixes of the original string, we can use a Suffix Array (后缀数组). Then we can simply compare the rank of the two suffixes in $\mathcal{O}(1)$.

The bottleneck in time complexity is building the Suffix Array, which is $\mathcal{O}(n\log n)$.

Code: https://qoj.ac/submission/413793

