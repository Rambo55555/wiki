**导入其它目录的包** 

```
import sys
sys.path.append("/home/rambo/GitHubWorkplace/Spaco")
```

**json美化输出**

```
import json
json.dumps(json, indent=4)
```

**Jupyter自动更新外部的包**

```python
%load_ext autoreload
%autoreload 2

```

