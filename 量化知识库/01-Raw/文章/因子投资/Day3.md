## 广义线性回归模型（generalized linear regression model）  
### 一、经典OLS的假设与现实的差距

经典OLS有一个核心假设：残差ε在每一期都独立、且方差相同（独立同方差）。用协方差矩阵表示就是：

E[εε'|X] = σ²I

这个矩阵是对角阵，对角线上全是同一个σ²，非对角线全是0（不同期的ε之间没有相关性）。

**但现实中这两个假设经常被打破：**

**异方差（heteroscedasticity）**：不同时期的ε方差不同。比如金融危机期间市场波动大，残差方差σₜ²远大于平静时期——每一期的"噪音大小"不一样。

**自相关（autocorrelation）**：不同时期的ε之间存在相关性。比如今天的残差为正（资产被低估），明天的残差也倾向于为正（低估持续存在）——相邻两期的噪音不独立。  
**εε' 是什么**

ε 是 T×1 的残差向量，εε' 是它和自己的转置相乘，得到一个 T×T 的矩阵：

εε′=[ε1ε2⋮εT][ε1ε2⋯εT]=[ε12ε1ε2⋯ε1εTε2ε1ε22⋯ε2εT⋮⋱⋮εTε1εTε2⋯εT2]\varepsilon\varepsilon' = \begin{bmatrix}\varepsilon_1\\\varepsilon_2\\\vdots\\\varepsilon_T\end{bmatrix}\begin{bmatrix}\varepsilon_1&\varepsilon_2&\cdots&\varepsilon_T\end{bmatrix} = \begin{bmatrix}\varepsilon_1^2 & \varepsilon_1\varepsilon_2 & \cdots & \varepsilon_1\varepsilon_T\\\varepsilon_2\varepsilon_1 & \varepsilon_2^2 & \cdots & \varepsilon_2\varepsilon_T\\\vdots & & \ddots & \vdots\\\varepsilon_T\varepsilon_1 & \varepsilon_T\varepsilon_2 & \cdots & \varepsilon_T^2\end{bmatrix}εε′=​ε1​ε2​⋮εT​​​[ε1​​ε2​​⋯​εT​​]=​ε12​ε2​ε1​⋮εT​ε1​​ε1​ε2​ε22​εT​ε2​​⋯⋯⋱⋯​ε1​εT​ε2​εT​⋮εT2​​​

对这个矩阵取期望，每个元素各自取期望：

- 对角线元素：E[εₜ²] = Var(εₜ)，第t期残差的方差
- 非对角线元素：E[εᵢεⱼ] = Cov(εᵢ, εⱼ)，第i期和第j期残差的协方差

所以 E[εε'|X] 就是残差的**协方差矩阵**，记为Σ。

**σ²I 意味着什么**

σ²I 展开就是：

![[Pasted image 20260627112553.png]]

对角线全是σ²，非对角线全是0。这同时表达了两件事：

**同方差**：对角线全相同，意味着每一期的残差方差都等于σ²，不管是平静期还是危机期，噪音大小完全一样。

**无自相关**：非对角线全是0，意味着任意两期的残差之间协方差为零，互不相关——今天的误差和明天的误差没有任何关系。

**一旦这两个假设任一被打破，等号右边就不再是σ²I，而是更一般的Σ=σ²Υ，这正是广义线性回归要处理的问题。**