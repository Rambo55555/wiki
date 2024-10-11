### 问题

记录一次使用Sbert模型出现对句子编码向量不一致的原因。问题复现：当使用以下代码对句子进行编码，得到的向量不一致，有细微差别。

```python
import numpy as np
from sentence_transformers import SentenceTransformer

model = SentenceTransformer("all-mpnet-base-v2")
model.eval()
a = model.encode(["hello","goodbye man"])
b = model.encode(["hello"])
print(np.array_equiv(a, b))
# output: false
print(a[0][:10], b[0][:10])
[-0.06277177  0.05495887  0.05216482  0.08579    -0.08274893 -0.07457298
  0.06855468  0.0183964  -0.08201137 -0.03738482] 
[-0.06277176  0.05495886  0.05216479  0.08578998 -0.08274891 -0.074573
  0.06855468  0.01839639 -0.08201136 -0.03738476]
```

网上找的解释：当编码多个句子时，所有的文本会使用pad填充到最长文本的长度，这些padding token会略微改变编码向量。https://github.com/UKPLab/sentence-transformers/issues/1356。但是感觉这个解释很牵强，自己又看了一下encode的源码，并没有pad操作。最简单的验证方法是保证最长文本长度一致，但是发现还是不一样。

后面发现把batch_size设置为1，编码出来的向量就是一致的了，看了是这个的问题。问了下GPT，说是浮点数运算顺序不同，结果不同。这个问题先暂时留在这。



