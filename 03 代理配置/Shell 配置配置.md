临时配置，终端输入：
```shell
export http_proxy=http://127.0.0.1:7897
export https_proxy=$http_proxy
export socks5_proxy="socks5://127.0.0.1:7897"
```

永久配置，配置到 `~/.bashrc` 中：
```shell
export http_proxy="http://127.0.0.1:7897"
export https_proxy=$http_proxy
export socks5_proxy="socks5://127.0.0.1:7897"
```

取消代理：
```shell
unset http_proxy
unset https_proxy
unset socks_proxy
```