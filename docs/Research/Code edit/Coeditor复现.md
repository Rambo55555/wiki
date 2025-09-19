C3problem数据集构造流程：

首先确定一个项目，拿到该项目的所有commits[0,...,n]，从第0个作为commit_now开始遍历，取它的后一个作为commit_next。针对commit_next中某一个变更的函数（修改的，**目前来看都是Modified类型**），取它的原始token序列作为original，在其上面进行的变更作为delta。那么每一个样本的目标是根据original以及相关变更relevant_changes和相关未变更relevant_unchanged，预测delata。delta和original组成了span

**需确定相关变更relevant_changes和相关未变更relevant_unchanged是如何得到的**

relevant_changes：一个排序后的ChangedCodeSpan列表

relevant_unchanged：一个dict，存放的是PyDefinition



取首次commit，拿到项目下的所有.py文件，针对每个文件，构建JModule。拿到后面的commit列表，开始往后移动，每次取一个commit_now+commit_next。

首先通过`git diff`拿到变更文件列表，遍历这些文件拿到path_changes: Added, Deleted, Modified。对于非Added类型，也就是Deleted和Modified存一份它们的module的拷贝，

