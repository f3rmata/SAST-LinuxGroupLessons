---
theme: seriph
title: Linux 进阶教程之容器技术
class: text-center
transition: slide-left
mdc: true
overviewSnapshots: true
---

# Linux 进阶教程之容器技术

软件研发部 运维组

---
layout: quote
---

## 引言

---
layout: quote
---

## 引言

- 什么是容器技术？
- 容器技术和虚拟化技术有什么区别？
- 容器技术和 wsl 有什么区别？
- 容器技术有哪些应用场景？

---
layout: quote
---

### 什么是容器技术？

容器技术是一种轻量级的操作系统层面虚拟化技术，为软件应用及其依赖组件提供一个资源独立的运行环境。在容器化过程中，应用程序及其所有必要的依赖关系会被打包成一个可重用的镜像。镜像所使用的资源在逻辑上独立，共享系统的内存、CPU 和硬盘空间，以保证容器内部进程与外部进程相互独立。

---
layout: quote
---

### 什么是容器技术？

<div style="display: flex; align-items: center; justify-content: space-between;">
  <div style="width: 45%; max-height: 300px; display: flex; flex-direction: column; align-items: center;">
    <img src="/容器抽象结构.jpg" alt="" style="width: 100%; max-height: 300px; object-fit: contain;">
    <div style="font-style: italic; color: gray;">*图片来自网络</div>
  </div>
  <div style="width: 50%; padding-left: 20px;">
    <p>
      容器相当于从操作系统中抽象出来的一个进程，它拥有自己的文件系统、网络、进程空间等资源。所有容器基于同一个 Kernel ，不同容器中可以运行不同的操作系统,在这些不同的操作系统中，也可以运行不同的应用程序。
    </p>
    <p>
      容器是层状的，每一层都是只读的，只有最上层是可写的。这样做的好处是可以共享底层的文件系统，节省存储空间。
    </p>
  </div>
</div>

---
layout: quote
---

## 引言

- <span style="color: gray; opacity: 0.7;">什么是容器技术？</span>
- 容器技术和虚拟化技术有什么区别？
- 容器技术和 wsl 有什么区别？
- 容器技术有哪些应用场景？

---
layout: quote
---

### 容器技术和虚拟化技术有什么区别？

<div style="display: flex; flex-direction: column; justify-content: center; align-items: center; height: 100%; text-align: center;">
  <img src="/容器和虚拟机区别.png" alt="" style="height: 300px; object-fit: contain;">
  <div style="font-style: italic; color: gray;">*图片来自网络</div>
</div>

对于所有容器来说，它们都是在同一个操作系统内核上运行的，因此容器的启动速度非常快，通常只需要几秒钟。容器的资源消耗也非常低，因为它们共享操作系统内核，而不是像虚拟机那样每个都有一个操作系统。

---
layout: quote
---

## 参考资料

- [容器 (虚拟化)](https://zh.wikipedia.org/wiki/%E5%AE%B9%E5%99%A8_(%E8%99%9A%E6%8B%9F%E5%8C%96)/)

---
layout: end
---

Thanks!
