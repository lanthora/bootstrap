# Tellus

## 为何存在

如果满足以下 __所有__ 条件,这个项目可能适合你:

* 有一台服务器,且自建了一些服务
* 服务部署后长期无需人工干预
* 担心服务器被入侵且无法发现入侵

如果不能满足上面的所有条件,那么把时间用在享受生活上吧.
又或者,只是单纯的爱折腾,那么请继续往下看.

## 名称由来

这个项目中没有任何有效代码,仅仅为了方便部署,把多个独立项目作为子模块放在了同一个项目中,实际功能由各个子模块实现.

子项目用罗马神话中的神命名, `Tellus` 作为罗马神话中的最初的神,刚好适合这个项目的名字.

## 如何编译

完整编译需要多个依赖,可以在子项目中获取完整依赖.
或者简单粗暴一些,直接 `make`,根据报错补全依赖后重新 `make`直到编译成功.

成功后会输出 _类似_ 以下内容:

```txt
========================= SUCCESS ========================
[1] /root/bootstrap/hackernel/kernel-space/hackernel.ko
[2] /root/bootstrap/hackernel/user-space/build/hackernel
[3] /root/bootstrap/uranus/cmd/sample/uranus-sample
[4] /root/bootstrap/uranus/cmd/telegram/uranus-telegram
[5] /root/bootstrap/uranus/cmd/web/uranus-web
==========================================================
```

* [1] 是内核模块,进行其他操作前需要[加载内核模块](https://man.archlinux.org/man/insmod.8.en).
* [2] 是与内核模块直接通信的服务程序,这个程序又对外提供接口,避免其他服务直接与内核交互.可以直接启动.
* [3] 作为测试程序,运行依赖上一个服务程序.运行后持续输出系统中启动的进程.这个程序正常即可确定所有服务正常启动.
* [4] 产生的日志上报到 Telegram,与 `uranus-sample` 同级
* [5] 支持 Web API 操作,与 `uranus-sample` 同级

有些进程启动时需要配置文件,配置文件参考子模块中的说明.

如果仔细观察编译过程,还会发现构建了前端项目,前端项目的文件直接被编译到了 [5] 的二进制中.
由于作者是前端的初学者,除用户登录登出外,并没有完成任何实际可用的功能.
如果你是前端开发人员,欢迎协助完成页面开发.

子模块将维持最新稳定版本,而非主线版本.

## 如何部署

执行 `make install` 把需要的模块都安装到系统中,此操作需要 root 权限.
内核模块依赖 `dkms`,需要预先安装.

安装成功后手动加载内核模块 `modprobe hackernel`,或直接重启.

内核加载成功后分别开两个窗口执行 `hackernel` 和 `uranus-sample`.
开启第三个窗口随意执行一些命令并观察 `uranus-sample` 的输出.
如果输出内容中有刚才执行的命令,代表测试通过.

服务类进程提供了 systemd service 文件:

* hackernel.service
* uranus-telegram.service
* uranus-web.service

以及对应的配置文件:

* /etc/hackernel/telegram.yaml
* /etc/hackernel/web.yaml

`make remove` 可以删除所有安装到系统中的文件.
