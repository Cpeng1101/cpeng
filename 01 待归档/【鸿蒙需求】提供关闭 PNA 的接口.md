## 需要搞明白的
### 什么是 PNA ？
在 Android WebView 和 Chrome（以及其他基于 Chromium 的浏览器）中，**PNA 指的是 Private Network Access（私有网络访问）**。用于控制从公共互联网（公网）加载的网页或 Web 应用访问位于私有网络（内网）或本地设备（`localhost`）上的资源的权限。

当一个**来自公网**的页面（源是公网域名或公网 IP）试图向一个**私有网络**资源（目标地址是私有 IP 或 `localhost`）发起跨域请求（如 Fetch API, XMLHttpRequest）时，浏览器会强制执行一个**预检请求**
- 这个预检请求是一个 `OPTIONS` 请求。
- 关键请求头： `Access-Control-Request-Private-Network: true`
- 目标服务器必须响应
	- `Access-Control-Allow-Private-Network: true`
	- 其他必要的 CORS 头（如 `Access-Control-Allow-Origin`, `Access-Control-Allow-Methods`, `Access-Control-Allow-Headers`）。

只有预检请求成功（返回 `200 OK` 且包含 `Access-Control-Allow-Private-Network: true` 以及其他必要的 CORS 头），浏览器才会允许后续的实际请求（`GET`, `POST` 等）发送到私有网络资源。如果预检失败，浏览器会阻止实际请求并报告 CORS（跨域资源共享）/PNA 错误。







https://pna-test-six.vercel.app/



使用 `http-server` 启动一个本地服务并开启 CORS:
1. 确保已安装 Node.js
2. 安装 http-server：`npm install -g http-server`
3. 启动服务器并启用 CORS：`http-server --cors` http://127.0.0.1:8080



本地测试服务器：
`node cors-server.js`
```js
const http = require('http');
const fs = require('fs');
const path = require('path');

// 创建 HTTP 服务器
const server = http.createServer((req, res) => {
  console.log(`[${new Date().toISOString()}] 收到请求: ${req.method} ${req.url}`);
  
  // 打印所有请求头
  console.log('请求头:');
  for (const [name, value] of Object.entries(req.headers)) {
    console.log(`  ${name}: ${value}`);
  }
  
  // 检测预检请求 (OPTIONS 方法)
  if (req.method === 'OPTIONS') {
    console.log('>>> 检测到预检请求 <<<');
    
    // 设置 CORS 响应头
    res.setHeader('Access-Control-Allow-Origin', '*');
    res.setHeader('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE, OPTIONS');
    res.setHeader('Access-Control-Allow-Headers', 'Content-Type, Authorization');
    res.setHeader('Access-Control-Max-Age', '86400'); // 预检结果缓存24小时
    
    // 响应预检请求
    res.writeHead(204); // No Content
    res.end();
    return;
  }
  
  // 处理实际请求
  res.setHeader('Access-Control-Allow-Origin', '*');
  
  // 根据请求路径提供文件
  const filePath = path.join(__dirname, req.url === '/' ? 'index.html' : req.url);
  
  fs.readFile(filePath, (err, data) => {
    if (err) {
      if (err.code === 'ENOENT') {
        res.writeHead(404);
        res.end('文件未找到');
      } else {
        res.writeHead(500);
        res.end('服务器错误');
      }
      return;
    }
    
    // 设置内容类型
    const extname = path.extname(filePath);
    let contentType = 'text/html';
    switch (extname) {
      case '.js': contentType = 'text/javascript'; break;
      case '.css': contentType = 'text/css'; break;
      case '.json': contentType = 'application/json'; break;
      case '.png': contentType = 'image/png'; break;
      case '.jpg': contentType = 'image/jpg'; break;
    }
    
    res.setHeader('Content-Type', contentType);
    res.writeHead(200);
    res.end(data, 'utf-8');
  });
});

// 启动服务器
const PORT = 8080;
server.listen(PORT, () => {
  console.log(`服务器运行在 http://localhost:${PORT}/`);
  console.log('等待请求... (按 Ctrl+C 停止)');
});
```

模拟服务器发送预检请求：
1. 在浏览器中打开任意网站
2. 按 F12 打开开发者工具
3. 在 Console 面板输入：
	```js
	fetch('http://localhost:8080/index.js', {
	  headers: {
	    'Content-Type': 'application/json' // 只保留必需的头
	  }
	});
	```