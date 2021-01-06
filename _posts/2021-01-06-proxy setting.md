# Git代理配置

## 现象

在配置git代理时，常使用以下命令：

```shell
git config --global http.proxy 'socks5://127.0.0.1:1080' 	# 端口号依本地代理端口而变
git config --global https.proxy 'socks5://127.0.0.1:1080'
# or
git config --global http.proxy 'http://127.0.0.1:1081' 		# 端口号依本地代理端口而变
git config --global https.proxy 'http://127.0.0.1:1081'
```

但是常常不生效，clone速度还是那么慢。考虑以下clone命令：

```shell
git clone git@github.com:llvm/llvm-project.git
```

其速度慢如蜗牛，非常让人着急，反复重新配置代理也不好使。

## 原因

问题并非出在http代理配置上，而是clone方法非http方法。上述clone命令是利用ssh进行数据传输的，所以并未使用http代理，所以速度很慢。

解决方法如下：

```shell
# 使用http链接进行clone
git clone https://github.com/radareorg/radare2.git
```

这样一来，就可以在配置了http代理的情况下实现高速clone了。

实际上，只需要配置`http.proxy`就可以了，不配置`https.proxy`也不影响github的clone速度。

```shell
# 优化方案：只对github代理，节约代理流量的同时避免在clone国内仓库时减慢速度
git config --global http.https://github.com.proxy 'socks5://127.0.0.1:1080' 
```

## 参考

[Git HTTP Proxy and SSH Proxy](https://plantegg.github.io/2018/03/14/%E5%B0%B1%E6%98%AF%E8%A6%81%E4%BD%A0%E6%87%82git%E4%BB%A3%E7%90%86--%E5%A6%82%E4%BD%95%E8%AE%BE%E7%BD%AEgit%20Proxy/)

## 注意

通过http方式clone的仓库，其远端地址也是http格式的，所以push的时候会要求输入用户名和密码。在`.git/config`中将远端地址改称ssh格式即可自动验证身份。