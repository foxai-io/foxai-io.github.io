---
layout: single
title:  "Mac 本地运行 stable diffusion"
date:   2023-04-20 23:07:09 +1300
categories: 教程Tutorial
tags: [学习记录, Stable Diffusion, Mac, CoreML, Apple Silicon]
permalink: /在Mac上搭建StableDiffusion
author: Jimmy狐狸
header:
  teaser: /assets/images/midjourney/midjourney-04-19-panda-1232x928.png
toc: true
toc_label: "内容大纲"
toc_sticky: true
---


这篇文章里，我打算记录我是如何一步步的在我的 MacBook Air M2 16GB 搭建 native 环境，下载模型，用苹果官方提供的方式跑 stable diffusion，做 AI Art Generation 。  
在尝试这个过程中，我踩了不少的坑，遇到很多错误，最终运行成功，这里记录下来，如果能对 Mac 用户有一点帮助，那就最好了。

# 背景介绍

此方案依赖 [苹果官方repo](https://github.com/apple/ml-stable-diffusion)，大家也可以看这篇文章 [CoreML Diffusers](https://huggingface.co/blog/diffusers-coreml) 进行参考。

优点：

- 本地运行，环境搭建好后，不依赖网络即可生成图片，
- 苹果官方支持并提供工具脚本，
- 全套方案免费、开源，
- 这套工具使用 CoreML，针对苹果新的 m1, m2 芯片做了适配，尤其是会针对性使用芯片里的 GPU 和 Neural Engine，让 stable diffusion 在 mac 上的性能变得可以接受，性能类似或优于使用 Docker 或云服务。

缺点：

- 仅支持 Apple Silicon，不支持老的 intel CPU，
- 没有官方UI，只能用现有的开源UI与此项目配合使用，
- Mac本地的 `Stable Diffusion` 只能看作是 Baby Version，Mac本身没有 `CUDA` 的支持，目前苹果 Apple Silicon 还无法兼容 或 完全替换 `Cuda` 指令，的确让本地的 Machine Learning 大受限制，`Stable Diffusion` 的威力减半。
- 目前没有看到能做 negative prompt, image2image, control net 等这些 advanced feature。

所以这套方案可以让你快速、免费入门，但是如果要专业级应用的话，最好的办法是用 `Google Colab` ，我也会在另外的教程中帮大家普及。

# 环境搭建

基础环境包括：

- python
- git


## python

MacOS 有系统默认的 Python, 我检查我的系统 MacOS Ventura 13.2.1 下默认有：`python3: 3.9.6`  
但是！永远不要用系统默认的 Python 来做开发，包括 Macchine Learning 的开发调试。

详细解释可见文章：<https://opensource.com/article/19/5/python-3-default-mac>

> "The basic premise of all Python development is to never use the system Python. You do not want the Mac OS X 'default Python' to be 'python3.' You want to never care about default Python."

ChatGPT 告诉我：

> Using the system Python for machine learning development is generally not recommended because it can lead to conflicts with the system packages and libraries. Installing a separate Python environment management tool, like pyenv, is a better approach as it allows you to create isolated environments and manage different Python versions easily.

安装升级Python最好的方式，是使用 `pyenv` . `pyenv` 不仅仅可以帮你管理不同版本的 `python` （机器里 python 环境乱 是一个很麻烦的事情），也可以为每一个项目开一个独立的 `python` 环境，这样不同项目的 `python` 环境就不会互相影响。

### 安装 `pyenv`

```shell
brew update
brew install pyenv
```

### 安装 `python`

现在可以用 `pyenv` 安装 `python` 了。

在 [官方网站](https://www.python.org/downloads/macos/) 上面看到，最新版本是 `3.11.3` 。但是！此版本目前没有被 `apple/coremltools` 支持，直接使用此版本的话，会在后面生成图片的步骤中报错。

[GitHub Issue](https://github.com/apple/coremltools/issues/1484) 这里看到，连 `Python 3.10` 版本的支持也是最近才加入的。苹果的工程师真的一点都不卷啊。

所以我们要安装的版本是 `3.10.11` 。

> Stable Releases
> Python 3.10.11 - April 5, 2023

确认版本后，运行命令：

```shell
$ pyenv install 3.10.11
$ pyenv global 3.10.11
```

设置 `shell` 环境：  
(假设你和我一样是用的 zsh，可以通过看 是否有 `~/.zshrc` 来确认)

```shell
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(pyenv init -)"' >> ~/.zshrc
exec "$SHELL"
```

确认安装成功：

```shell
$ pyenv version
3.10.11 (set by /Users/jimmy/.pyenv/version)
$ python --version
Python 3.10.11
```

### pyenv-virtualenv

`pyenv-virtualenv` 是 python 开发必不可少一个工具，用来给每一个项目配备独立的虚拟环境，也可以用于 `conda` 。安装很简单，运行以下命令：

```shell
brew install pyenv-virtualenv
echo 'if which pyenv-virtualenv-init > /dev/null; then eval "$(pyenv virtualenv-init -)"; fi' >> ~/.zshrc
```

## git环境

`git` 是代码库的版本管理工具，由于我们的模型或脚本工具都是在 GitHub / Huggingface 这样基于 Git 的托管网站上管理的，`git` 就是必需品。大模型文件的下载更需要 `git lfs` 的帮助。

如果你安装过 xcode，`git` 应该是伴随 `xcode` 安装好的，用下面的命令确认：（如果没有安装的话，运行命令也会提示安装。）

```
$ git --version
git version 2.39.2 (Apple Git-143)
```

安装`git lfs`:

```
$ brew install git-lfs
$ git lfs install
```

好了！基础工具最重要的就这些，现在我们下载模型。

# 模型下载

## 苹果的 stable diffusion 脚本工具

下载苹果的 ml stable diffusion 工具。这个工具是接下来主要使用的工具。

[Apple ML SD Repo](https://github.com/apple/ml-stable-diffusion)

运行指令：

```
$ git clone https://github.com/apple/ml-stable-diffusion
$ cd ml-stable-diffusion
$ mkdir models
```


## Stable Diffusion 基础模型

普通的 stable diffusion 模型在 mac 上是无法使用的，我们需要下载 CoreML 转换过版本才行。（我们也可以自己在本地转换模型为 CoreML 版本，但苹果已经有了一些转换好的模型，拿来用即可。）  
要找一个合适的模型，上 [huggingface](https://huggingface.co/apple)。

我现在看到版本号最新的是 [2.1-base 版本](https://huggingface.co/apple/coreml-stable-diffusion-2-1-base)，

打开 termina，找到合适的文件夹里，运行命令：（需要注意的是，每个variant的模型大小接近 2GB，一个repo下就有4个 variant，要保证磁盘上有足够的空间）

```
$ git clone https://huggingface.co/apple/coreml-stable-diffusion-2-1-base
```

记住这个地址。

模型下载后，其文件夹结构大概是这样的：

```
coreml-stable-diffusion-2-1-base  
├── original  
│   ├── compiled              # Swift inference, "original" attention  
│   └── packages              # Python inference, "original" attention  
└── split_einsum  
    ├── compiled              # Swift inference, "split_einsum" attention  
    └── packages              # Python inference, "split_einsum" attention  
```

这个模型有 4 个 variant，其中区别：

- `original` vs `split_einsum`: `split_einsum` 可以兼容所有的计算单元（CPU、GPU、Neural Engine），而 `original` 仅支持 CPU GPU。
- `compiled` vs `packages`: `compiled` 是 `swift` 语言版本，`packages` 是 `python` 版本。

我在这里使用 `split_einsum` + `packages`.

## Run

### 将模型移入工具目录

假设这两步里 你下载的模型和工具是在平级目录，运行下面的指令把模型挪到工具的models目录中：

```
$ mv coreml-stable-diffusion-2-1-base/split_einsum/packages ml-stable-diffusion/models/coreml-stable-diffusion-2-1-base_split_einsum_packages
```

### 启动新的 python virtualenv

```
$ pyenv virtualenv 3.10.11 apple-sd-env
$ pyenv activate apple-sd-env
(apple-sd-env) jimmy@Jimmys-Air
```

### Huggingface

这里我们需要安装huggingface的工具，然后在 huggingface 上创建 账户，创建 access code，并在本地登陆huggingface。

![在 hugging face 创建 access token](assets/images/04-20-post/huggingface.jpg)
<p style="text-align:center">在 hugging face 创建 access token</p>

完成后，在本地运行指令：

```
$ pip install --upgrade huggingface_hub
$ huggingface-cli login
```

在指令提示的时候，粘贴 access token 并回车。

在运行 sd 前，我们还需要配置 huggingface 的本地cache文件夹。运行指令：

```
$ mkdir ~/.cache/huggingface
$ mkdir ~/.cache/huggingface/hub
$ sudo chmod 777 ~/.cache/huggingface/
$ sudo chmod 777 ~/.cache/huggingface/hub
```

这里需要确保我们将要运行的 huggingface 脚本有 写入cache的权限。

*TODO：网上看到有可能指定 cache目录到其他的地址，但我没有尝试成功。脚本无论如何也要访问 ~/.cache/huggingface/hub*

### 运行 stable diffusion

到了这里，我们应该已经准备就绪，可以跑SD指令了。  
进入目录：`ml-stable-diffusion` ，首先确认模型到位。

我们指定了目录地址：`models/coreml-stable-diffusion-2-1-base_split_einsum_packages` ，确保刚才有将模型挪入这个地址，该地址下有如下文件：

```
Stable_Diffusion_version_stabilityai_stable-diffusion-2-1-base_text_encoder.mlpackage
Stable_Diffusion_version_stabilityai_stable-diffusion-2-1-base_unet.mlpackage
Stable_Diffusion_version_stabilityai_stable-diffusion-2-1-base_unet_chunk1.mlpackage
Stable_Diffusion_version_stabilityai_stable-diffusion-2-1-base_unet_chunk2.mlpackage
Stable_Diffusion_version_stabilityai_stable-diffusion-2-1-base_vae_decoder.mlpackage
Stable_Diffusion_version_stabilityai_stable-diffusion-2-1-base_vae_encoder.mlpackage
```

运行指令：

```
$ pip install diffusers
$ pip install -r requirements.txt  
$ python -m python_coreml_stable_diffusion.pipeline --prompt "a photo of an astronaut riding a horse on mars" --compute-unit ALL -o output --seed 93 -i models/coreml-stable-diffusion-2-1-base_split_einsum_packages --model-version stabilityai/stable-diffusion-2-1-base
```

没有问题的话，会看到目录 output 下应该有生成的图片了。

![SD生成的第一张图片](assets/images/04-20-post/randomSeed_93_computeUnit_ALL_modelVersion_stabilityai_stable-diffusion-2-1-base.png)
<p style="text-align:center">SD生成的第一张图片</p>

# 后记与TODO

这篇blog只是一个开始，希望我能继续下去 不断自我学习与更新文章，也希望能有更多人能参与进来，一起讨论学习。

近期的blog里，我会继续探索：

- 这个苹果官方脚本的更多功能和prompt
- 其他简单一点的 stable diffusion 方案，比如 `Google Colab`
- ...
