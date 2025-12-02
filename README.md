# ProxyClient

一个功能强大的代理客户端库，支持多种代理协议。本项目重构自 [github.com/RouterScript/ProxyClient](https://github.com/RouterScript/ProxyClient)。

blog posts:

- https://chainreactors.github.io/wiki/blog/2025/02/14/proxyclient-introduce/

## 支持的协议

- [x] Direct - 直连模式
- [x] Reject - 拒绝连接
- [x] Blackhole - 黑洞模式
- [x] HTTP - HTTP 代理
- [x] HTTPS - HTTPS 代理
- [x] SOCKS5 - SOCKS5 代理
- [x] ShadowSocks - ShadowSocks 代理
- [x] SSH Agent - SSH 代理
- [x] Suo5 - Suo5 协议
- [x] Neoreg - Neoreg 协议

## 基本使用

### 快速开始

```go
package main

import (
	"fmt"
	"io/ioutil"
	"net/http"
	"net/url"
	"github.com/kelesec/proxyclient"
)

func main() {
	// 创建代理URL
	proxy, _ := url.Parse("http://localhost:8080")

	// 创建代理客户端
	dial, _ := proxyclient.NewClient(proxy)

	// 创建HTTP客户端
	client := &http.Client{
		Transport: &http.Transport{
			DialContext: dial.Dial,
		},
	}

	// 发送请求
	resp, err := client.Get("http://www.example.com")
	if err != nil {
		panic(err)
	}
	defer resp.Body.Close()

	// 读取响应
	content, _ := ioutil.ReadAll(resp.Body)
	fmt.Println(string(content))
}
```

### Example

#### Curl 

模拟 curl 命令，通过代理访问指定的 URL。

```bash
go build ./example/curl
curl <proxy-url> <target-url>

# 示例
./curl http://127.0.0.1:8080 https://example.com
```

#### NC 

模拟 nc 命令，通过代理连接指定的主机和端口。

```bash
go build ./example/nc
./nc <proxy-url> <target-host> <target-port>

# 示例
./nc http://127.0.0.1:8080 example.com 80
```

#### SOCKS5 

在本地启动一个 SOCKS5 服务器，将所有流量通过上游代理转发。

```bash
go build ./example/socks5
./socks5 <proxy-url> <listen-addr>

# 示例
./socks5 http://127.0.0.1:8080 :1080
```

## 协议配置说明

### HTTP/HTTPS

HTTP 和 HTTPS 代理支持基本认证。

```
格式：http(s)://[username:password@]host:port
参数：
- username: 认证用户名
- password: 认证密码
- host: 代理服务器地址
- port: 代理服务器端口

示例：
http://user:pass@127.0.0.1:8080
https://127.0.0.1:8443
```

### SOCKS5

支持无认证和用户名密码认证两种方式。

```
格式：socks5://[username:password@]host:port
参数：
- username: 认证用户名（可选）
- password: 认证密码（可选）
- host: 代理服务器地址
- port: 代理服务器端口

示例：
socks5://127.0.0.1:1080
socks5://user:pass@127.0.0.1:1080
```

### ShadowSocks

支持多种加密方式。

```
格式：ss://method:password@host:port
参数：
- method: 加密方式，支持：aes-256-gcm, chacha20-ietf-poly1305等
- password: 密码
- host: 服务器地址
- port: 服务器端口

示例：
ss://aes-256-gcm:password@127.0.0.1:8388
```

### Suo5

Suo5 协议支持多种参数配置。

```
格式：suo5(s)://host:port/path?param1=value1&param2=value2
参数：
- timeout: 连接超时时间，如：5s
- retry: 重试次数，默认10
- interval: 读写间隔，如：100ms
- buffer_size: 缓冲区大小，默认32KB

示例：
suo5://example.com:8080/tunnel?timeout=10&retry=5
suo5s://example.com:8443/tunnel?interval=200ms
```

### Neoreg

Neoreg 协议支持丰富的参数配置。

```
格式：neoreg(s)://key@host:port/path?param1=value1&param2=value2
参数：
- key: 连接密钥（必需）
- timeout: 连接超时时间，如：5s
- retry: 最大重试次数，默认10
- interval: 读写间隔，如：100ms
- buffer_size: 读取缓冲区大小，默认32KB

示例：
neoreg://password@example.com:8080/tunnel?timeout=10s
neoregs://password@example.com:8443/tunnel?interval=200ms&retry=5
```

### 注意事项

- 对于需要 TLS 的协议，可以通过在协议名后添加's'来启用：`https://`, `suo5s://`, `neoregs://`
- 部分协议支持通过 URL 参数进行高级配置

## 参考

- [GameXG/ProxyClient](https://github.com/GameXG/ProxyClient)
- [RouterScript/ProxyClient](https://github.com/RouterScript/ProxyClient)

## 补充说明

此项目是在 github.com/chainreactors/proxyclient 的基础上进行构建，以便于适配自己的工具库，致敬原作者🫡🫡🫡
