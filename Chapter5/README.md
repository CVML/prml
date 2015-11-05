在3，4两章中，我们讨论了固定基函数的线性组合构成的回归和分类模型。我们看到这样的模型具有有用的分析与计算性质，但是在实际应用中它们被维数灾难问题限制了。为了将这些模型应用于大规模的问题，需要根据数据调节基函数。    

第7章中讨论的支持向量机（SVM）首先定义以训练数据点为中心的基函数，然后在训练过程中选择子集来决解这个问题。SVM的一个优点是尽管训练涉及非线性优化，目标函数是凸的，因此最优问题的解相对直接的。虽然通常来说最终模型中基函数的数量很多，且通常随着数据规模的增加而增加，但是它远小于训练数据点的数量。相关向量机（7.2节中讨论）同样从基函数固定集合中选择子集，通常会生成一个相当稀疏的模型。与SVM不同，它也产生概率形式的输出，虽然这会导致训练阶段的非凸优化开销。    

另一种方法是事先固定基函数的数量，但是允许调节它们，也就是说使用在训练阶段可调节的参数形式的基函数。在模型识别中，这种方法最成功的模型是将在本章讨论的前馈神经网络，也被称为多层感知器（multilayer perceptron）。实际上，“多层感知器”是一个相当不正确的命名，因为模型是由多层logistic回归模型（带有连续的非线性性质），而不是由多层感知器（带有非连续的非线性性质）组成。对于很多应用来说，得到的模型比具有同样泛化能力的支持向量机简洁的多，因此计算的速度更快。这种简洁性的代价是，与相关向量机一样，构成了网络训练根基的似然函数不再是模型参数的凸函数。然而，在实际应用中，考察模型在训练阶段消耗的计算资源来得到一个简洁的模型是很有价值的，它可以快速地处理新数据。

术语“神经网络”来源于它尝试寻找生物系统中的信息处理的数学表示（McCulloch and Pitts, 1943; Widrow and Hoff, 1960; Rosenblatt, 1962; Rumelhart et al., 1986）。实际上，这个模型已经被广泛使用，涵盖了相当多的不同种类的模型，许多模型过分夸张地宣称其具有生物学的似真性。然而，从模式识别的实际应用角度来说，模仿生物的真实性会带来相当多的不必要的限制。因此，本章中，我们把注意力集中于作为统计模式识别的高效模型的神经网络。特别地，我们要把注意力集中于已经被证明有相当大的实用价值的神经网络的一个具体的类别上，这就是神经网络的多层感知器。    

我们以考虑神经网络的函数形式，包括基函数的具体参数开始。然后我们讨论使用最大似然框架确定神经网络参数的问题，这涉及到非线性最优化问题的解。这种方法需要计算对数似然函数关于神经网络参数的导数，我们看到这些导数可以使用误差反向传播（error backpropagation）的方法高效的获得。我们还会说明误差反向传播的框架如何推广到计算其它导数，例如Jacobian矩阵和Hessian矩阵。接下来，我们讨论神经网络训练的各种正则化方法，及方法之间的关系。我们还会考虑神经网络模型的一些扩展。特别地，会描述一个被称为混合密度网络（mixture density network）的通用框架来对条件密度建模。最后，我们讨论神经网络的贝叶斯观点运用。额外的关于神经网络模型的背景可以参考Bishop(1995a)。