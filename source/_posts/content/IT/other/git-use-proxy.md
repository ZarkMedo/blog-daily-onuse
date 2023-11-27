---
author: "Medo"
title: "GitHub访问过慢，如何解决"
date: "2018-04-13"
summary: "gitHub 被拦截怎么办"
tags: ["随记","工程问题"]
series:
  - 随记
categories:
  - IT Tech
  - Other
cover: https://jsd.cdn.zzko.cn/gh/ZarkMedo/photoBed@master/img/20231127203655.png
---

## git访问过慢解决方案

### 使用镜像源
#### 方案一：油猴插件
[github增强-高速下载](https://greasyfork.org/zh-CN/scripts/412245-github-%E5%A2%9E%E5%BC%BA-%E9%AB%98%E9%80%9F%E4%B8%8B%E8%BD%BD)
- 安装后会出现额外的镜像下载源，不用代理也可以快速上传和下载
![展示](github-proxy-greasyfork.png)
> 需要浏览器有油猴扩展才行
#### 方案二：浏览器扩展
> [edge浏览器扩展-github加速](https://microsoftedge.microsoft.com/addons/detail/github%E5%8A%A0%E9%80%9F/alhnbdjjbokpmilgemopoomnldpejihb)
- 和油猴插件的原理一样，和使用方式类似，edge浏览器不受限制，可以直接安装。
  
### git客户端指定代理
> 前提需要你有代理服务器
#### windows的设置与取消
```cmd
rem 设置代理, 使用http或者sock协议都可以, 二者选其一
rem sock5协议
git config --global http.proxy socks5h://127.0.0.1:10808
git config --global https.proxy socks5h://127.0.0.1:10808
rem http协议
git config --global http.proxy http://127.0.0.1:10808
git config --global https.proxy http://127.0.0.1:10808


rem 取消全局代理
git config --global --unset http.proxy
git config --global --unset https.proxy
rem 查看当前全局的http代理
git config --global --get http.proxy
git config --global --get https.proxy
```