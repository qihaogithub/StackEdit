
从你提供的终端记录来看，核心问题是 **Bun 在安装 `opencode-antigravity-auth` 时遇到了 SSL 证书验证错误** (`UNKNOWN_CERTIFICATE_VERIFICATION_ERROR`)，导致安装失败。因为安装没成功，所以系统找不到 `opencode` 命令。

这种情况通常是因为网络环境（如代理、公司防火墙或在某些地区访问 npm 官方源不稳定）导致的。

请按照以下步骤尝试修复：

### 1. 使用国内镜像源（推荐）
SSL 错误通常是访问 `registry.npmjs.org` 不顺畅导致的。切换到淘宝镜像（npmmirror）通常能直接解决：

```bash
bun add opencode-antigravity-auth@latest -g --registry=https://registry.npmmirror.com
```

### 2. 在同一行命令中强制禁用 SSL 验证
虽然你执行了 `export`，但有时 Bun 的二进制逻辑可能没有正确捕捉到。尝试将环境变量直接写在命令前：

```bash
NODE_TLS_REJECT_UNAUTHORIZED=0 bun add opencode-antigravity-auth@latest -g --force
```

### 3. 如果还是不行，改用 npm 安装
如果 Bun 的网络模块由于某些原因持续报错，可以临时改用 `npm`（Node.js 自带的工具）来全局安装。`opencode` 本身是 JS 写的，用 npm 安装效果是一样的：

```bash
npm install -g opencode-antigravity-auth@latest
```
*(如果遇到权限问题，请在前面加 `sudo`)*

### 4. 确认 PATH 路径
安装成功后，如果依然提示 `command not found`，说明 Bun 的全局二进制目录没有添加到你的系统路径中。

请检查并在你的 `~/.zshrc` 文件末尾添加以下行（如果你使用的是 Bun）：
```bash
export PATH="$HOME/.bun/bin:$PATH"
```
然后运行 `source ~/.zshrc` 生效。

### 5. 重新运行并登录
安装成功后，再次尝试：
```bash
opencode auth login
```

---

### 为什么之前会失败？
*   **SSL 错误**：你的网络环境无法验证 npm 官方服务器的证书。这在开启了某些 VPN 或处于受限网络时很常见。
*   **Bun 缓存**：你之前尝试过 `bun pm cache clear`，但因为网络验证在缓存之前就失败了，所以清除缓存没有解决根本问题。
*   **命令找不到**：因为 `bun add ... -g` 报了 `error`，所以它压根没有把可执行文件下载到你的电脑上。

**建议先尝试第 1 步（镜像源），这在 90% 的情况下能解决国内用户的此类问题。**
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTQ2MDk2NTA0MiwtMTU5NzU3Nzk5MiwtMT
c5OTUxMDUwMSwtMTgxMTczMzQyNl19
-->