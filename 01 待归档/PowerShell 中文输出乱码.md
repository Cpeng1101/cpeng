## 查看字符编码

```shell
chcp

# 65001 为 UTF-8
# 936 为 GBK
```

## 临时方法

终端输入：

```shell
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8
```

## 永久方法

1. 查看当前用户配置文件所在路径：
	```
	echo $PROFILE
	```

2. 编辑配置文件，在末尾添加：
	```shell
	$OutputEncoding = [console]::InputEncoding = [console]::OutputEncoding = New-Object System.Text.UTF8Encoding
	```