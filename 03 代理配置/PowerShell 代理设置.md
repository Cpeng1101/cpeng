获取配置文件路径：
```shell
echo #PROFILE

# C:\Users\10964\Documents\PowerShell\Microsoft.PowerShell_profile.ps1
```

配置文件末尾添加：
```shell
function set_proxy_variable {
	Set-Item Env:http_proxy "http://127.0.0.1:7897"  # 代理地址
	Set-Item Env:https_proxy "http://127.0.0.1:7897" # 代理地址
}

function unset_proxy_variable {
    Remove-Item Env:http_proxy
    Remove-Item Env:https_proxy
}

New-Alias -Name spp -Value set_proxy_variable
New-Alias -Name upp -Value unset_proxy_variable
```

- spp：设置网络代理；
- upp：取消代理。