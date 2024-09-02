# vllm安装踩坑

vllm官网说是要cuda的版本为12.1，但是我的是12.0也装成功了，python命令行和py文件能用，但是.ipynb用不了，记录一下。

官网安装链接：https://docs.vllm.ai/en/latest/getting_started/installation.html

查看本地cuda版本：`nvcc -V`

本地CUDA版本：12.0

python版本：3.10

torch版本：2.4.0+cu121



安装过程：

先安装的CUDA + python + torch 

然后使用命令：`pip install vllm`安装vllm

安装完之后想在jupyternotebook试下效果，尝试以下代码：

```python
from vllm import LLM

llm = LLM(model="meta-llama/Meta-Llama-3-8B-Instruct")  # Name or path of your model
output = llm.generate("Hello, my name is")
print(output)
```

报错：

libcusparse.so.12: undefined symbol: __nvJitLinkAddData_12_1, version libnvJitLink.so.12

按网上博客解决：https://blog.csdn.net/qq_42730750/article/details/139582293

重启jupyter内核，还是无法导入torch。但是使用python命令行可以导入torch，后尝试在py文件里面直接使用

```
import os
os.environ['HF_ENDPOINT']='https://hf-mirror.com'
os.environ['HF_TOKEN']='###'
import torch
from vllm import LLM

if __name__ == '__main__':
    print(torch.cuda.is_available())
    token = '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'
    llm = LLM(model="meta-llama/Meta-Llama-3-8B-Instruct")  # Name or path of your model
    output = llm.generate("Who are you")
    print(output)
    output = llm.generate("What's the next president of the United States")
    print(output)
```

很奇怪。。。。