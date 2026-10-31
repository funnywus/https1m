# https1m

给网站一张浏览器信任的 SSL 证书，大约 1 分钟。

**在线体验：[https://ssl.ccrate.cc](https://ssl.ccrate.cc)**

Let’s Encrypt 签发，浏览器信任，90 天有效。到期前 15 天自动续签。本仓库只提供说明，请直接打开上面的地址试用。

## 流程

```
注册账号 → 填写域名 → 加一条 CNAME → 约 1 分钟出证 → 下载或一条命令部署
```

### 1. 注册

打开 [https://ssl.ccrate.cc](https://ssl.ccrate.cc)，邮箱注册并登录。申请记录、额度、部署都跟这个账号走。

### 2. 申请证书

进入控制台，点 **免费申请**。域名不要加 `http://`。

| 填写 | 结果 |
| --- | --- |
| `example.com` | 默认按通配符签 `*.example.com`，根域和一级子域名都能用 |
| `*.example.com` | 同上 |

### 3. 添加 CNAME

页面会给出主机记录和记录值，到域名 DNS 加 **一条 CNAME**：

| 类型 | 主机记录 | 记录值 |
| --- | --- | --- |
| CNAME | `_acme-challenge` | 页面上的 `xxxx.auth.ccrate.cc` |

常见面板：腾讯云、DnsPod、阿里云、华为云、Cloudflare。Cloudflare **不要开小云朵（代理）**。

这条记录只需配一次，续签也靠它，加好后不要删。

### 4. 等待出证

解析生效后大约 1 分钟签发。页面变成「已签发」后，可下载 Nginx / Apache / Windows 等格式。

同一域名续签不占用新额度。

### 5. 部署到服务器（可选）

登录后打开 **自动部署**，复制一行命令，在网站服务器用 **root** 执行。看到 `全部完成` 即可。

## 自动部署脚本

脚本由站点下发，不放在本仓库，保证始终是线上最新版。凭证在登录后的「自动部署」页，跟随账号，不要公开。

### 一条命令

```bash
curl -fsSL https://ssl.ccrate.cc/httpsly.sh | bash -s -- 你的部署凭证 bt
```

把最后的 `bt` 换成你的环境：

| 参数 | 用途 |
| --- | --- |
| `bt` | 宝塔。写入对应站点，打开面板 SSL，reload |
| `nginx` | Nginx。证书写入 `/etc/nginx/ssl` 并 reload |
| `apache` | Apache。证书写入 `/etc/ssl/httpsly` |
| `caddy` | Caddy。证书写入 `/etc/caddy/certs` |
| `files` | 只落到 `/etc/ssl/httpsly`，不 reload |

第一次执行会：

1. 扫描并拉取这个账号下的证书  
2. 一行一行写入网站（带百分比）  
3. 登记这台机器  
4. 重载 Web 服务  
5. 打开每天自动续签  

本机已有的宝塔 / Let’s Encrypt 证书也会同步进平台，之后可在「证书申请」里看到。

### 以后怎么用

```bash
~/.httpsly/httpsly
```

| 数字 | 作用 |
| --- | --- |
| 1 | 拉取全部证书并写入 |
| 2 | 写入站点 SSL 配置 |
| 3 | 重载 Web 服务 |
| 4 | 修改类型 / 目录 / 凭证 |
| 5 | 查看状态 |
| 6 | 开关每天自动拉取 |
| 7 | 查看日志 |
| 8 | 同步本机证书到平台 |
| 0 | 退出 |

每天 03:15 会静默拉取一次。机器会出现在控制台的 **主机管理**。

## 说明

- 证书来自 Let’s Encrypt，浏览器信任  
- CNAME 验证，不需要在网站根目录放文件  
- 免费额度计入登录账号  
- 本仓库不开源服务端代码，体验请用 [https://ssl.ccrate.cc](https://ssl.ccrate.cc)
