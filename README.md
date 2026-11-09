# github-setup

Windows 11 本机 Git / GitHub 配置与 SSH 代理隧道记录。

## 环境

| 项 | 值 |
|---|---|
| 系统 | Windows 11 专业版 22631 |
| Git | 2.55.0（便携版，位于 `D:\Tools\Git`） |
| gh CLI | 2.103.3（`D:\Tools\gh\bin`） |
| 身份 | `zsx886z` / `15520071761@163.com` |
| SSH 密钥 | ed25519，带口令，载入 Windows ssh-agent（服务自启） |

## 网络链路

本机直连 GitHub 被拒，所有流量经本地代理 `127.0.0.1:7890`（出口为日本东京节点）：

- **HTTPS**：`~/.gitconfig` 中 `http.proxy` / `https.proxy` 指向 `127.0.0.1:7890`
- **SSH**：`~/.ssh/config` 中 `ProxyCommand` 调用自写的 HTTP CONNECT 隧道
  （`D:\Tools\netrelay\connect.js`，Node 实现，约 2KB），目标 `ssh.github.com:443`，
  不依赖 22 端口

```
git push/clone
   └─ ssh.exe（~/.ssh/config）
        └─ node connect.js 127.0.0.1:7890   ← HTTP CONNECT 隧道
             └─ 代理东京节点
                  └─ ssh.github.com:443 → GitHub
```

## 备用通道（~/.ssh/config 中的别名）

| 别名 | 走法 | 适用场景 |
|---|---|---|
| （默认）`git@github.com:...` | 代理隧道 → 443 | 代理开启时 |
| `git@github-direct:...` | 直连 22 | 代理关闭且 22 通 |
| `git@github-443:...` | 直连 443 | 代理关闭且 443 通 |

## 验收记录

- [x] `ssh -T git@github.com` → `Hi zsx886z! You've successfully authenticated`
- [x] gh CLI 设备码授权登录成功
- [x] 本仓库经 SSH 隧道 clone 成功
- [x] 本文件提交并 push 成功（首个真实写入）
