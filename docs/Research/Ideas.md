## 背景

开源代码大模型有效促进了软件智能化开发这一过程，学术界和工业界在积极探索如何利用提示工程、上下文学习、检索增强以及各种后处理技术来训练、微调和增强大模型在各种软件工程下游任务的能力。

## Q&A

1. 当前代码大模型主要是在通用大模型基础上继续训练得到的还是完全重新训练的？

A. 代码训练为主：StarCoder、DeepSeek-Coder（先训的代码，再训的通用大模型）、CodeQwen（？）、aiXcoder、CodeGen

B. 通用模型基础上继续训练：CodeLlama

B优势：NLP和Math能力更强，代码能力稍弱。代码模型通常在混合代码+垂域数据的SFT，同时会在一些下游任务上进行对齐；尤其是对于常用开发场景，需要进行进一步的语料扩充与训练；同时类似PPO/DPO的方式进行进一步的偏好对齐能够使得模型快速拟合实际偏好。

2. 代码大模型的训练和微调有哪些常用方法，其有效性如何？

预训练常用方法：Next token prediction（NTP）和Fill in the middle（FIM）任务。NTP任务让模型学习代码数据的特性、代码数据和自然语言的对应等，以具有预测下一个token的能力。FIM任务让模型学习给定代码上下文的前提下，如何预测需要补全的代码。

微调内容：

- 指令微调（Instruction Fine-tuning）：通过在指令数据集上对模型进行微调来显著增强模型遵循指令的能力。例如Self-Instruct和Evol-Instruct利用大模型来直接生成微调数据。这两种方式生成的微调数据容易受到模型偏见的影响（比如模型只会生成比较常见的指令数据）。OSS-Instruct（张令明教授团队提出的）：利用开源代码来引导大模型生成更加多样、真实和可控的微调数据。OSS-Instruct以及我们基于此微调的Magicoder模型在GitHub上已经有1900多star。OSS-Instruct也已经被许多其它代码大模型采用，比如最近的Google CodeGemma、IBM Granite Code Models、StarCoder2-Instruct等等。Snowflake Arctic、Meta Llama3以及Amazon Q等团队也在尝试使用OSS-Instruct进行代码模型的微调。
- 上下文微调：（Long-context Fine-tuning）通过在长序列数据上进行训练并对结合位置编码改进（例如Position Interpolation）来提升模型在长下文代码生成上的能力。
- 其它信息：代码结构信息、数据流、语法树。在小一些的预训练模型上，前人尝试过不同的训练任务（如GraphCodeBERT考虑数据流、UniXcoder考虑语法树的任务等），但目前（貌似）还没有adapt到大模型里，训练效率可能是主要原因。以前另外有些代码大模型在训练时对代码仓库进行拓扑排序解析文件之间的依赖，被证明可以增强长距离跨文件的代码理解能力。

微调方式：包括全量微调、Adapter tuning、LORA、QLORA、Prefix tuning、Prompt tuning、P-tuning等，不考虑资源限制的话，全量微调效果好，但大多情况下，资源都很受限，因此现在各种PEFT（参数高效微调）方法非常流行。



**小问题**：代码大模型的训练和微调技术能否应用于文本嵌入模型？目前一个有意思的初步发现，似乎用基于通用大模型得到的文本代码嵌入模型，比专门基于代码大模型的文本嵌入模型要好。可能源于通用大模型的文本理解能力在这方面更有优势。



2. 企业在开展面向特定领域的代码大模型微调与定制时可以采取哪些技术手段（例如微调方式和任务）？在数据准备和清洗以及其他关键问题上有哪些需要特别注意的问题？



领域数据CT/PEFT/RAG等都是可以用的办法，一些其他的Superalignment的技术也可以起到辅助作用。

技术手段：特定领域的数据预训练或者微调、PEFT、RAG、Superalignment的技术。预训练和微调的作用是不一样的，最近有一篇关于LLM微调和幻觉关系的研究，结论大致是大模型主要通过预训练获得事实知识，而微调则教会它们更有效地使用这些知识。如果在微调过程中引入新的事实知识，可能会增加大模型产生幻觉的风险。不过，仍需要注意灾难性遗忘问题。我们之前的实验表明，虽然使用特定领域的代码语料进行了继续预训练，然后再进行微调，最终效果不如直接基于基础模型进行微调。

数据问题：领域数据很重要，理想做法是以全链条开发数据作为原料，但通常企业难以提供这样的数据，也就是说，很多企业在以往的开发中没有对相关的开发数据进行有效的管理，软件制品之间的关系也没有理顺，这是数据治理阶段要解决的任务。在数据治理过程中应该针对不同任务设计不同的数据治理方案，而不应该在不管下游任务的情况下，盲目收集数据，这将直接关系到后续任务的进行。方法：数据去重、选取高星项目的代码、选择可读性更高的代码（如含有较高比例注释的代码）。启发式规则、代码去重、依赖解析、静态缺陷扫描。

- 数据质量：确保训练数据的质量，包括数据的准确性、完整性和一致性。

- 数据代表性：最好能涵盖领域内的各种用户情况和场景。避免数据偏差和过拟合。

- 数据平衡：确保各类别的数据分布平衡，避免类别不平衡问题，这个需要对数据有一个完整的类目体系建设。

- 数据清洗：去除训练数据中的噪声、重复数据和无关信息，可以通过一些工具或者reward model进行打分。

- 数据隐私：在处理敏感数据时，确保遵守相关法规和政策，对数据进行脱敏处理。

- 模型泛化能力：通过数据增强等方法提高模型的泛化能力，避免过拟合。

- 模型评估：选择合适的评估指标和体系模型进行全面评估，例如实际场景中humaneval往往是不够用的。



3. 企业在代码大模型落地应用时，如何将提示工程、上下文学习、多Agent、检索增强以及各种后处理技术与模型微调有机结合，从而提高代码推荐和生成的准确性？

多Agent技术：将开发过程链接起来，通过与大模型的多环节多方式的交互完成开发任务。李戈教授团队文章：Self-collaboration Code Generation [TOSEM,2024] ,CodeAgent [ACL 2024], Prompt with Actor-Critic Editing[ACL 2024]等。爆火的可刷榜的：SWE-Bench、Devin

适用于代码推荐和生成、测试用例生成、Bug修复、Code Review等场景。

RAG：使用RAG，在prompt中包含最近打开或编辑的文件内容、当前文件所在路径，以及通过静态分析或embedding检索的方式对当前代码仓进行相似和关联代码检索，以提供给模型更多的上下文信息；改进RAG，在RAG代码生成的过程中，通过强化学习来微调检索模型，改进以前只检索相似代码带来的问题，如检索器依赖有标签数据、候选代码块语义不完整、不加选择地检索等问题，参考王焱林老师团队。

后处理技术：过滤或修复有明显语法错误的代码、对缩进和代码格式进行修正等。



4. 企业在代码大模型落地应用时有哪些比较成功的应用场景（例如代码补全/生成/重构以及软件开发问答等）？

代码自动补全、代码生成、研发问答（技术知识答疑、技术知识搜索、疑难问题解决、技术方案输出等等）；

代码生成、代码缺陷自动检测 或 代码检视、测试用例自动生成、代码重构、代码缺陷修复、代码注释生成、代码故障分析。

存在问题：

- 简单场景如片段性的代码能独立完成，但是复杂业务场景中的表现就不尽如人意。使用的时候补全粒度和上下文依赖不能特别大。例如LLM在类级别的代码生成任务上表现很差。
- 在一些维护类型任务中，大模型的表现也还不理想，比如添加特性和修复复杂的BUG等任务。由于这些任务的上下文特别复杂，目前即使是号称最先进的基于Agent的技术，效果也只有20%左右，并且还依赖于很多理想化的假设。特别是大模型目前输入窗口的限制和长上下文理解能力不强的现状，制约了其大模型的效果。

**为了实现软件开发质量和效率提升，需要如何度量代码推荐和生成的质量（例如采纳率或其他指标）？**

普通指标：EM、ES,Pass@k、BLEU、CodeBLEU 采纳率、编译通过率、代码留存度

常用指标：

- 采纳率。体现了用户对模型推荐的满意度，质量更高的推荐、不打扰用户的推荐更可能会被采纳

- 自动生成占比。是一个“实测指标”，而不是一个“基于已知结果”计算而来的指标。这个指标融合了很多权衡的结果，例如代码生成长度、代码推荐速度等多方面。体现了代码助手对用户开发效率的提升，代码生成占比越高，说明越能帮助用户提升效率，因为这样用户更多的tab就ok了。

- CPO（Character per Opportunity）。通过计算每个机会中平均字符数来衡量的代码补全工具的表现。机会指的是用户在IDE中进行操作的每个动作，例如输入或删除一段代码。CPO的计算公式是CPO = 尝试率 * 反馈率 * 采纳率 * 每次采纳平均token数 * 每token的平均字符数。尝试率是指在每次可能给用户补全代码的机会中，工具实际为用户提供建议的频率，弥补了“不推荐就没有拒绝”的问题。反馈率是指有多少工具生成的补全建议最终被展示给了用户，当网络延迟过高、用户持续键入时，会导致较低的反馈率。采纳率即为用户实际采纳了多少工具推荐的代码。每次采纳平均token数反映了每条被采纳建议所传递出的实际价值，而每token的平均字符数可以平衡不同模型的分词器导致的toekn可以代表过长或者过短字符串的问题。
- 工具补全的代码实际被合入代码仓的比例。
- 平均采纳代码行数。往往推荐出来的多行且高质量的内容，能大大提升效率，并且使得用户更加信任代码助手，例如当模型能正确地推荐一个函数、一个for循环的时候，用户会觉得代码助手更加智能。

可能指标：

-  开发时间，因为单点指标高（如函数级代码生成的采纳率高），不一定意味着总开发时间会降低，可能采纳了某段代码后，程序员还需要额外的时间去理解和调试模型生成的代码。

- 基于testcase的正确性反馈/人工的评估反馈，实际很难做到；

  

**还需要考虑哪些人机交互（例如如何掌握代码补全和推荐的时机）及其他方面的问题？**

- 产品的体验会在很大程度上影响用户的接受率，正确的触发时机也会影响推理成本以及用户体验，产品侧可以结合如意图识别/生成请求判别等简单模型来优化体验，这些都是可以考虑的。
- 代码补全和推荐的时机也是另一个非常重要的问题，主要体现在不打扰用户编码过程中的思考，通义灵码针对性地分析了一些日常使用场景中遇到的不良触发时机，在这些时机不触发补全模型，并且在模型侧也增加模型对一些场景判断补全为空的能力，从而减少对用户的打扰；这一方面灵码也在持续地优化中。

- 仅就代码补全而言，通常需要考虑的相关问题包括：推荐反馈速度、推荐长度、多推荐结果的处理等。除此之外还需要考虑如何多GPU处理并发，如何收集错误反馈等问题。

- 一些面向用户的个性化可能是需要考虑，比如推荐结果考虑用户的编码习惯、命名风格、习语使用、三方库偏好等等，这个可能可以提高人员对于解决的可理解性和接受度。另外一个方面，生成结果的可读性可能是需要考虑，如果开发者看不懂推荐结果，可能就完全不会采纳了。

- 代码补全和推进的时机不应该是人机交互考虑的，而是代码服务（工程层及模型层）考虑的，对用户行为的识别与判断的考验，同时考虑千人千面，每个用户的行为习惯/思维习惯、组织规范要求等等。



5. 当前企业应用代码大模型主要是完成一些简单任务（例如局部的代码补全与生成），那么代码大模型是否有可能支持更加复杂的开发任务（例如更大范围内的生成式软件开发以及大规模软件的维护型任务等）？为此需要解决哪些关键问题，有哪些初步的思路和方向？

关键问题：

- 强大的基座模型支持和更好的模型编排和微调都很重要。单纯靠现有的大模型设计和scaling law去完全取代人类程序员是不太现实的，因为现有模型基本只能模拟人脑中非常小的一部分语言处理和生成区域。

- 当前模型在处理更为复杂的仓库级代码任务时仍面临挑战，例如仓库级问题修复、代码生成方面。在问题修复数据集SWE-bench的leaderboard上，榜一Amazon Q Developer Agent也只有13.82%的通过率。利用RAG的GPT-4模型也仅取得了1.31%的通过率。需要提升代码大模型的大代码仓理解能力、任务规划和分解能力，以及继续提升局部的代码生成能力对仓库级能力的提升应当也有帮助。目前主流的思路是采用多智能体技术来解决仓库级别问题修复任务中的上述问题，并可以针对性的提升long context理解能力、相关代码检索能力、错误定位能力等。

- 比较大的可能也是现在大家在尝试的就是基于Agent的思路。但是这方面应该还有不少难点，例如大模型如何更好地使用工具，理解各种工具反馈，遵循复杂指令的能力。长上下文理解能力，多轮对话后的历史遗忘问题等等都是需要解决的。这些都需要模型的基础能力有更大的提升。

- 针对一些小点，目前一个可能的思路是通过针对性的微调来对应增强能力，比如收集各种工具的反馈构造指令微调数据，加强大模型对于这些反馈的理解能力。以及基于工具反馈去让大模型能力与工具反馈对齐等等。个人认为支持大范围软件生成和软件维护是可能的。实现的途径包括利用多Agent对复杂任务进行拆解和规划，结合RAG和一些成熟的分析工具，来协同完成最终目标。除了相关算法的设计之外，其中还有很重要的一点是对相关软件制品的数字化和知识化。例如，在对复杂软件进行维护型任务时需要对相关代码及所涉及到的业务知识进行全面的理解，这些知识经常分散在整个软件制品的各个部分（代码、文档、配置文件等）之中，大模型或Agent需要获取和整合这些信息来进行更好地决策和答案生成。因此，我们需要构建一套能够对复杂软件制品进行细粒度语义标注、建立知识之间的追溯关系、且支持知识同步演化的 “代码数字孪生”平台，这也是彭老师一直倡导的。

- 当前学术界和工业界也在积极探索将agent技术和代码检索、静态分析等结合，来实现完整的feature开发和issue解决，我们也预期基于大模型的技术可以实现整个工程的重构或者语言迁移。为了实现这些目标，有一些问题需要解决，包括任务的拆解和规划能力、复杂上下文的理解能力、生成代码的正确性和一致性等。任务的拆解和规划能力是指将复杂的大任务分解成一系列可管理的小任务，并为每个小任务制定详细的执行计划，或者在已有的计划中选择最优的计划去执行。这种能力对于软件开发尤其重要，因为开发过程通常包含多个相互关联的步骤和任务。优化的方向可以包括针对任务分解的模型训练、构建包含软件开发领域知识的知识图谱等。复杂上下文的理解能力是指在复杂的软件开发和维护中，对代码和文档的理解能力，需要通过改进注意力机制、层级模型等方式来优化。为了提升生成代码的正确性和一致性，我们可以考虑通过开发更适合大模型的轻量级代码分析工具、形式化验证等方式最模型生成的代码进行验证，并通过反馈和迭代的形式让模型生成更高质量的代码。

- 目前强模型+多agent协同的方式在当前的情况下有一定解决问题的能力，但是在更复杂的任务上，表现尚未达到预期；在代码模型上目前还停留在纯语言模型的阶段， 大胆设想一下多Agent协同 +（代码）多模态 + 执行沙箱 的模型可能会是一个解决的问题的方向，难点在于可能需要用test driven的方式出发，并且如何一步一步的引导智能体向目标逼近。

- 复杂的开发任务一般是开发者日常开发过程中的项目级别的开发任务，包括分析用户/产品提出的需求、组织搭建或者修改项目树结构、特定文件中增加/删除/修改对应的代码片段、寻找项目中的bug和异常等任务，这些任务需要模型能力进一步提升，不仅仅理解单个代码文件，而是理解项目结构、理解项目架构和逻辑、将用户需求对应到需要修改的代码片段上、对异常错误能知道如何定位和修复，这些能力需要rag、multi-agents、模型self-evolution、execution feedback等技术的支持，通义灵码在这些方面正在探索中，并且会将最新的成果集成到IDE中。如最近上线的库内问答和lingma workspace功能，针对用户需求在项目中查找和修改代码。

- 未来是完全有可能完成更加复杂的开发任务的。想要达成这种效果我认为还需要解决如何把更多的项目相关的需求、代码、issue等等知识给到大模型，并且让大模型理解这些知识并进行任务拆解、完成具体的设计生成、代码生成、用例生成等子任务。

 


