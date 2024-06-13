# CMG方法复现

## NNGen

给定`query diff`，将其和训练集中的所有`diff`变成词袋模型（Bag of Words）向量，通过计算`query diff`向量和其它向量的余弦相似度，找到最相似的top-k个commits对应的`messages`，再把`query diff`和这些`messages`计算BLEU分数，选择最高的作为检索结果。

