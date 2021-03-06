降低具有大量权值参数的网络复杂度的一种方法是将权值分组,然后令分组内的权值相等。这是5.5.6节中讨论的将网络对于图像的平移不变性整合到网络的构建过程中的权值共享的方法。然而，它只适用于限制的形式可以事先确定的问题中。这里，我们考虑软权值共享（soft weight sharing）（Nowlan and Hinton,
1992）。这种方法中，权值相等的硬限制被替换为一种形式的正则化，其中权值的分组倾向于取近似的值。此外，权值的分组、每组权值的均值，以及分组内的取值范围全都作为学习过程的一部分被确定。    

回忆一下，式（5.112）给出的简单的权值衰减正则化项可以被看成权值上的高斯分布的负对数。我们可以将权值分为若干组，而不是将所有权值分为一个组。分组的方法是使用高斯混合概率分布。混合分布中，每个高斯分量的均值、方差，以及混合系数，都会作为可调节的参数在学习过程中被确定。于是，我们有形式为

$$
p(w) = \prod\limits_i p(w_i) \tag{5.136}
$$

的概率密度。其中    

$$
p(w_i) = \sum\limits_{j=1}^M \pi_j \mathcal{N}(w_i|\mu_j,\sigma_j^2) \tag{5.137}
$$

$$ \pi_j $$为混合系数。取负对数，即可得到形式为

$$
\omega(w) = -\sum\limits_i \ln \left(\sum\limits_{j=1}^M\pi_j \mathcal{N}(w_i|\mu_j,\sigma_j^2)\right) \tag{5.138}
$$

正则化函数。从而，总的误差函数为

$$
\tilde{E}(w) = E(w) + \lambda\omega(w) \tag{5.139}
$$

其中，$$ \lambda $$是正则化系数。这个误差函数同时关于权值$$ w_i $$和混合模型参数$$ \{pi_j, \mu_j, \sigma_j\} $$进行最小化。如果权值是常数,那么混合模型的参数可以由第9章讨论的EM算法确定。然而，权值分布本身在学习过程中是不断变化的，因此为了避免数值的不稳定性，我们同时关于权值和混合模型参数进行最优化。可以使用标准的最优化算法（例如共轭梯度法或拟牛顿法）来完成这件事。    

为了最小化总的误差函数，计算出它关于各个可调节参数的导数是很有必要的。为了完成这一点，比较方便的做法是把$$ \{\pi_j\} $$当成先验概率，然后引入对应的后验概率。根据式（2.192），后验概率由贝叶斯定理给出的形式为

$$
\gamma_i(w) = \frac{\pi_j\mathcal{N}(w|\mu_j,\sigma_j^2)}{\sum_k\pi_k\mathcal{N}(w|\mu_k,\sigma_k^2)} \tag{5.140}
$$

这样，总误差函数关于权值的导数为

$$
\frac{\partial\tilde{E}}{\partial w_i} = \frac{\partial E}{\partial w_i} + \lambda\sum\limits_j\gamma_j(w_i)\frac{(w_i - \mu_j)}{\delta_j^2} \tag{5.141}
$$

于是，正则化项的效果是使用大小为正比于对于给定权值的高斯分布的后验概率的力把每个权值拉向第$$ j $$个高斯分布的中心,拉力。这恰好就是我们要寻找的效果。     

误差函数关于高斯分布的中心的导数也很容易的得到结果：

$$
\frac{\partial\tilde{E}}{\partial\mu_j} = \lambda\sum\limits_i\gamma_j(w_i)\frac{(\mu_i-w_j)}{\sigma_j^2} \tag{5.142}
$$

它具有简单的直观含义，因为它使用大小为第j个高斯分量产生的权值参数的后验概率的力把$$ \mu_j $$拉向了权值的平均值。类似地，关于方差的导数为    

$$
\frac{\partial\tilde{E}}{\partial\sigma_j} =  \lambda\sum\limits_i\gamma_j(w_i)\left(\frac{1}{\sigma_j} - \frac{(w_j - \mu_j)^2}{\sigma_j^3}\right) \tag{5.143}
$$

它将$$ \sigma_j $$拉向权值在对应的中心$$ mu_j $$附近的偏差的平方的加权平均，加权平均的权系数与之前一样，等于由第$$ j $$个高斯分量产生的权值参数的后验概率。注意，在实际执行过程中，我们会引入一个由

$$
\sigma_j^2 = exp(\xi_j) \tag{5.144}
$$

定义的新变量$$ \xi_j $$。且，最小化的过程是关于$$ \xi_j $$进行的。这确保了参数$$ \sigma_j $$是正数。此外，它还能够倾向于避免找到病态解，即一个或多个$$ \sigma_j $$趋于零，对应高斯分量退化为权参数的值。9.2.1节会在高斯混合模型的问题中详细讨论这样的解。    

对于关于混合系数$$ \pi_j $$的导数，需要考虑限制条件：

$$
\sum\limits_j\pi_j = 1, 0 \leq \pi_j \leq 1 \tag{5.145}
$$

这个限制的产生，是因为我们把$$ \pi_j $$看成了先验概率。可以这样做：将混合系数通过一组辅助变量$$ \{\eta_j\} $$用softmax函数表示，即

$$
\pi_j = \frac{exp(\eta_j)}{\sum_{k=1}^Mexp(\eta_k)} \tag{5.146}
$$

这样，正则化的误差函数关于$$ \{\eta_j\} $$的导数的形式为

$$
\frac{\partial\tilde{E}}{\partial\eta_j} = \sum\limits_i\{\pi_j - \gamma_j(w_i)\} \tag{5.147}
$$

我们看到，$$ \pi_j $$被拉向第$$ j $$个高斯分量的平均后验概率。
