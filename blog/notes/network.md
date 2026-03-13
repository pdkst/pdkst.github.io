# 计算机网络

计算机网络是现代技术的基础，理解网络原理对开发和调试都很有帮助。

## OSI 七层模型

| 层级 | 名称 | 功能 | 协议 |
|------|------|------|------|
| 7 | 应用层 | 为应用程序提供服务 | HTTP, FTP, SMTP, DNS |
| 6 | 表示层 | 数据格式化、加密 | SSL/TLS, JPEG |
| 5 | 会话层 | 建立、管理会话 | RPC, SMB |
| 4 | 传输层 | 端到端传输 | TCP, UDP |
| 3 | 网络层 | 路由选择 | IP, ICMP, ARP |
| 2 | 数据链路层 | 帧传输、错误检测 | Ethernet, Wi-Fi |
| 1 | 物理层 | 比特流传输 | RJ45, 光纤 |

## TCP/IP 模型

```
应用层      HTTP, FTP, SMTP, DNS
传输层      TCP, UDP
网络层      IP, ICMP, ARP
网络接口层  Ethernet, Wi-Fi
```

## 常用协议

### HTTP

HTTP (Hypertext Transfer Protocol) 是应用层协议，用于传输 Web 资源。

#### HTTP 方法

| 方法 | 说明 | 请求体 | 安全 | 幂等 |
|------|------|--------|------|------|
| GET | 获取资源 | 否 | ✅ | ✅ |
| POST | 创建资源 | 是 | ❌ | ❌ |
| PUT | 更新资源 | 是 | ❌ | ✅ |
| DELETE | 删除资源 | 否 | ❌ | ✅ |
| PATCH | 部分更新 | 是 | ❌ | ❌ |
| HEAD | 获取头部 | 否 | ✅ | ✅ |
| OPTIONS | 获取可用方法 | 否 | ✅ | ✅ |

#### HTTP 状态码

| 类别 | 状态码 | 说明 |
|------|--------|------|
| 1xx | 100 Continue | 继续请求 |
| 2xx | 200 OK | 成功 |
|      | 201 Created | 已创建 |
| 3xx | 301 Moved Permanently | 永久重定向 |
|      | 302 Found | 临时重定向 |
| 4xx | 400 Bad Request | 错误请求 |
|      | 401 Unauthorized | 未授权 |
|      | 403 Forbidden | 禁止访问 |
|      | 404 Not Found | 未找到 |
| 5xx | 500 Internal Server Error | 服务器内部错误 |
|      | 503 Service Unavailable | 服务不可用 |

#### HTTP vs HTTPS

```
HTTP:  明文传输，默认端口 80
HTTPS: 加密传输（SSL/TLS），默认端口 443
```

### TCP vs UDP

| 特性 | TCP | UDP |
|------|-----|-----|
| 连接 | 面向连接 | 无连接 |
| 可靠性 | 可靠传输 | 不可靠 |
| 速度 | 较慢 | 较快 |
| 流量控制 | 有 | 无 |
| 拥塞控制 | 有 | 无 |
| 应用场景 | Web, 邮件, 文件传输 | 视频, 游戏, DNS |

### DNS

DNS (Domain Name System) 将域名解析为 IP 地址。

#### DNS 查询过程

```
1. 检查浏览器缓存
2. 检查系统缓存 (hosts 文件)
3. 检查路由器缓存
4. 查询 DNS 服务器
   - 递归查询
   - 迭代查询
```

#### DNS 记录类型

| 类型 | 说明 | 示例 |
|------|------|------|
| A | IPv4 地址 | example.com → 93.184.216.34 |
| AAAA | IPv6 地址 | example.com → 2606:2800:220:1:248:1893:25c8:1946 |
| CNAME | 别名 | www.example.com → example.com |
| MX | 邮件服务器 | example.com → mail.example.com |
| TXT | 文本记录 | SPF, DKIM |

## 网络地址

### IP 地址

#### IPv4

```
格式: 192.168.1.1
范围: 0.0.0.0 - 255.255.255.255
```

#### IPv6

```
格式: 2001:0db8:85a3:0000:0000:8a2e:0370:7334
简化: 2001:db8:85a3::8a2e:370:7334
```

### 子网掩码

```
IP:    192.168.1.0
掩码:   255.255.255.0 (/24)
网络:   192.168.1.0
广播:   192.168.1.255
范围:   192.168.1.1 - 192.168.1.254
```

### 常用端口

| 服务 | 端口 | 协议 |
|------|------|------|
| HTTP | 80 | TCP |
| HTTPS | 443 | TCP |
| SSH | 22 | TCP |
| FTP | 20, 21 | TCP |
| SMTP | 25, 587 | TCP |
| POP3 | 110 | TCP |
| IMAP | 143 | TCP |
| DNS | 53 | TCP/UDP |
| MySQL | 3306 | TCP |
| Redis | 6379 | TCP |

## 网络工具

### ping

```bash
# 测试网络连通性
ping google.com

# 指定次数
ping -c 4 google.com

# 指定大小
ping -s 1000 google.com
```

### traceroute

```bash
# 追踪数据包路径
traceroute google.com

# Windows 使用 tracert
tracert google.com
```

### nslookup

```bash
# 查询 DNS
nslookup google.com

# 指定 DNS 服务器
nslookup google.com 8.8.8.8
```

### netstat

```bash
# 查看网络连接
netstat -an

# 查看监听端口
netstat -tln

# 查看进程信息
netstat -tlnp
```

### curl

```bash
# 发送 HTTP 请求
curl https://api.example.com

# 查看响应头
curl -I https://example.com

# POST 请求
curl -X POST -d "key=value" https://api.example.com

# 添加请求头
curl -H "Content-Type: application/json" https://api.example.com
```

### ssh

```bash
# 远程登录
ssh user@192.168.1.100

# 指定端口
ssh -p 2222 user@192.168.1.100

# 密钥登录
ssh -i ~/.ssh/id_rsa user@192.168.1.100

# 端口转发
ssh -L 8080:localhost:80 user@server
```

## Web 安全

### HTTPS

HTTPS 通过 SSL/TLS 加密 HTTP 流量：

```
HTTP (明文) → SSL/TLS 加密 → HTTPS (密文)
```

#### SSL/TLS 握手过程

```
1. Client Hello: 客户端发送支持的加密套件
2. Server Hello: 服务器选择加密套件并发送证书
3. 验证证书: 客户端验证服务器证书
4. Key Exchange: 交换密钥
5. Finished: 开始加密通信
```

### CORS

CORS (Cross-Origin Resource Sharing) 控制跨域资源访问：

```http
// 服务器响应头
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
Access-Control-Allow-Headers: Content-Type, Authorization
```

### XSS

XSS (Cross-Site Scripting) 跨站脚本攻击：

**防护措施：**
- 输入验证和过滤
- 输出转义
- 使用 CSP (Content Security Policy)

### CSRF

CSRF (Cross-Site Request Forgery) 跨站请求伪造：

**防护措施：**
- CSRF Token
- SameSite Cookie 属性
- 验证 Referer 头

## 常见问题

### 连接超时

```
原因：
1. 网络延迟
2. 服务器过载
3. 防火墙阻止

解决：
1. 检查网络连接
2. 增加超时时间
3. 检查防火墙配置
```

### DNS 解析失败

```
原因：
1. DNS 服务器故障
2. DNS 配置错误
3. 网络问题

解决：
1. 更换 DNS 服务器（8.8.8.8, 1.1.1.1）
2. 清除 DNS 缓存
3. 检查 hosts 文件
```

### 端口被占用

```bash
# 查看端口占用
netstat -tlnp | grep 8080

# 或使用 lsof
lsof -i :8080

# 终止进程
kill -9 PID
```

## 参考资料

- [计算机网络自顶向下方法](https://book.douban.com/subject/26960678/)
- [HTTP/MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTTP)
- [TCP/IP 详解](https://book.douban.com/subject/4248188/)

---

*最后更新时间: {docsify-updated}*