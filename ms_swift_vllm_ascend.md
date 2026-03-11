# ms-swift配置

## 镜像下载
- A3： docker pull quay.io/ascend/cann:8.5.1-a3-ubuntu22.04-py3.11
- A2： docker pull quay.io/ascend/cann:8.5.1-910b-ubuntu22.04-py3.11

一定要是python==3.11 版本，新版本vllm-ascend依赖trition-ascend，triton-ascend当前未提供 3.11 以上版本按照

## 镜像拉起

容器拉起命令
```
CONTAINER_NAME=ms_swift
DOCKER_IMAGE=quay.io/ascend/cann:8.5.1-a3-ubuntu22.04-py3.11
docker run -itd --pids-limit -1	\
    --privileged \
    --cap-add=SYS_PTRACE \
    --net=host \
    --device=/dev/davinci0 \
    --device=/dev/davinci1 \
    --device=/dev/davinci2 \
    --device=/dev/davinci3 \
    --device=/dev/davinci4 \
    --device=/dev/davinci5 \
    --device=/dev/davinci6 \
    --device=/dev/davinci7 \
    --device=/dev/davinci_manager \
    --device=/dev/devmm_svm \
    --device=/dev/hisi_hdc \
    --shm-size=1200g \
    -v /usr/local/sbin/npu-smi:/usr/local/sbin/npu-smi \
    -v /usr/local/dcmi:/usr/local/dcmi \
    -v /etc/ascend_install.info:/etc/ascend_install.info \
    -v /usr/local/Ascend/driver:/usr/local/Ascend/driver \
    -v /home:/home \
    --name "$CONTAINER_NAME" \
    "$DOCKER_IMAGE" \
    /bin/bash
```

进入容器

```
docker exec -it ms_swift /bin/bash
```

## cann配置
```
source /usr/local/Ascend/ascend-toolkit/set_env.sh
source /usr/local/Ascend/nnal/atb/set_env.sh
```

## vllm安装

```
pip install vllm==0.14.0 -i https://mirrors.aliyun.com/pypi/simple/
```

## vllmascend安装


前置依赖整理

```
pip uninstall trition


pip install torch==2.9.0 torch_npu==2.9.0 -i https://mirrors.aliyun.com/pypi/simple/
```


```
git clone -b v0.14.0rc1 https://github.com/vllm-project/vllm-ascend.git
cd vllm_ascend 
git submodule update --init --recursive
pip install -v -e . -i https://mirrors.aliyun.com/pypi/simple/
```


安装完毕查验一下torch和torch_npu的版本有没有被变动

```
pip list | grep torch
```

## 修正一下部分依赖

```
pip install torchvison==0.24.1 -i https://mirrors.aliyun.com/pypi/simple/
```


## ms-swift安装

```
cd ms-swift 
pip install -e . -i https://mirrors.aliyun.com/pypi/simple/
```


## 拉起推理

```
swift rollout --model /home/wjq/Qwen2.5-7B-Instruct --vllm_tensor_parallel_size 4
```
