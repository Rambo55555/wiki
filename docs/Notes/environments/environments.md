# 环境配置

## conda安装

```bash
https://docs.conda.io/projects/miniconda/en/latest/

~/miniconda3/bin/conda init
conda create --name d2l python=3.9 -y
conda activate d2l
pip install torch==1.12.0
pip install torchvision==0.13.0
pip install d2l==0.17.6

git config --global user.name "Mona Lisa"
git config --global user.email "YOUR_EMAIL"

# generate ssh
ssh-keygen -o
cat ~/.ssh/id_rsa.pub

#~/.ssh/config
Host github.com
  Hostname ssh.github.com
  Port 443
  
# 测试连接
ssh -T git@github.com

# 服务器设置ssh密钥免登录
vim ~/.ssh/authorized_keys
复制id_rsa.pub文件内容

# git bash环境切换conda环境
conda init
conda activate a

```

## conda目录迁移

https://blog.csdn.net/qq_34342853/article/details/123020957

## Hugging face internet problem

```python
    os.environ["http_proxy"] = "http://127.0.0.1:7890"
    os.environ["https_proxy"] = "http://127.0.0.1:7890"

HF_ENDPOINT=https://hf-mirror.com python train.py +model=codet5
```

## Hugging face cache problem
```bash
conda env config vars set XDG_CACHE_HOME=/mnt/ssd2/rambo/.cache
conda env config vars list

# 迁移缓存
mv /home/rambo/.cache/huggingface /mnt/ssd2/rambo/.cache/huggingface
```


## pip国内镜像

```
pip config set global.index-url https://mirrors.aliyun.com/pypi/simple/
pip config set install.trusted-host mirrors.aliyun.com
```

## 查用Linux命令

```
# 查看进程占用
ps -f -p 26359

# 脚本激活conda环境
eval "$(conda shell.bash hook)"
conda activate chronile

# 后台运行脚本
nohup .sh &
nohup X.sh >output.log 2>&1 & # 内置文件描述符0表示输入，1表示输出，2表示错误，这里的2>&1表示标准错误也输出标准输出中，如果是2>1表示输出到文件名为1的文件，而&1表示stdout
tail -fn 50 nohup.out

# 杀死进程
ps aux | grep 'rambo'
kill PID

# 内存信息
free -h

# 查看后台运行进程
jobs -l
```

# 终端命令行快捷键

- 清除当前行：`ctrl + u`
- 清除当前屏幕：`ctrl + l`

# 迁移环境

```
cd /home/user/anaconda3/envs
scp -r username@ip:"/home/user/anaconda3/envs/python310" ./
```

