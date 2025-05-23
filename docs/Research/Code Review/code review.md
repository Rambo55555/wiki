

## 代码评审中的挑战



代码评审是指有经验的开发人员对其他开发人员提交的代码进行仔细检查的过程。LLMs的快速发展为在一定的人为干预和指导下实现代码自动评审带来了新的机遇。然而，由于缺乏代码评审的知识库，代码评审严重依赖于特定的需求和目标，代码评审的完全自动化和智能化还无法实现。LLMs在代码评审应用中的挑战主要体现在4个方面：（1）缺乏对高质量代码评审意见的评估和提取；（2）针对不同类型问题定制的代码评审应用程序的构建；（3）行业和开源社区的代码评审实践之间的差距；（4）代码评审过程的端到端自动化

（1）缺乏对高质量代码评审意见的评估和提取。与代码生成等任务相比，现有的LLMs缺乏对代码评审任务的优化。有必要使用高质量的代码评审特定数据对LLM进行微调，使其能够更好地适应代码评审任务。然而，在实际项目中，代码评审数据的质量参差不齐。例如，评审意见包含大量无效评论(例如, "同上"和"参考前文评论")，导致目标和表达不明确。因此，设计一种评估方法来选择高质量的代码评审数据，并提出一种优化方法来细化低质量的代码评审意见至关重要。

（2）针对不同类型问题定制的代码评审应用程序的构建。在现实场景中，提交评审的代码变更往往涉及各种问题，包括安全漏洞、违反编码规范以及代码格式化问题[。现有的基于LLMs的代码评审应用将所有问题类型作为通用代码评审来处理，这导致LLMs主要专注于发现更常见的问题，例如规范和格式化问题，从而难以检测更严重的问题。此外，由于有监督微调的性质以及一组代码变更与单个代码评审意见之间的对应关系，为不同类型的问题构建代码评审应用是一个重大挑战

（3）减少行业代码评审实践与开源社区之间的鸿沟。现有的与LLMs相关的代码评审研究主要集中于开源软件项目。然而，开源社区和工业界的代码评审实践存在差异。具体来说，行业中的开发人员最关心的是直接影响开发人员和产品生存能力的安全问题，而开源社区项目涉及的安全问题更少，格式化和文档相关的问题更多样。因此，解决关键的行业需求，获取基于行业内部数据的LLMs后训练，并有效利用提示工程等技术进行推理，是基于行业的代码评审应用的关键挑战

（4）代码评审过程的端到端自动化。当前的代码评审自动化技术主要关注评审过程中的特定任务。这些技术主要基于深度学习和预训练模型，要求在特定数据集上进行微调以满足不同下游任务的需求，并且按阶段地完成代码评审过程。在LLMs时代，实现代码审查过程的完全自动化已经成为一个探索性的挑战。例如，当开发人员提出需求时，LLMs可以从需求中生成问题，定位相应的代码，自动生成修复方案，最终实现从需求提出到问题解决的端到端的自动化和智能化。

Gao, Cuiyun, et al. "The Current Challenges of Software Engineering in the Era of Large Language Models." *ACM Transactions on Software Engineering and Methodology* (2024).



ICSE 2025

Tufano, Rosalia, et al. "Deep Learning-based Code Reviews: A Paradigm Shift or a Double-Edged Sword?." *arXiv preprint arXiv:2411.11401* (2024).

