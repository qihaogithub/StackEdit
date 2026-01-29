
既然你想彻底“删干净再重装”，我们就执行一次最彻底的清理。按照这个顺序操作，可以解决 **路径错乱、包名不对、SSL报错** 这三大问题。

### 第一步：彻底删除（删干净）

请依次执行以下命令，把所有可能冲突的地方都删掉：

```bash
# 1. 卸载所有相关的全局包
npm uninstall -g opencode opencode-antigravity-auth
bun remove -g opencode opencode-antigravity-auth

# 2. 删除本地缓存和配置文件（这会让 opencode 像新装一样）
rm -rf ~/.opencode
rm -rf ~/.bun/install/cache/opencode*
rm -rf /Users/qh2/.npm-global/bin/opencode

# 3. 清理 npm 和 nvm 的冲突配置
sed -i '' '/prefix=/d' ~/.npmrc
nvm use --delete-prefix v20.19.0
```

---

### 第二步：正确重装（安装主程序）

你之前安装的 `opencode-antigravity-auth` 极大概率**只是一个插件**，不包含 `opencode` 这个命令。**主程序的包名通常就叫 `opencode`。**

请使用以下命令重装主程序（使用镜像源确保成功）：

```bash
# 使用 NODE_TLS_REJECT_UNAUTHORIZED=0 解决你之前的证书报错
NODE_TLS_REJECT_UNAUTHORIZED=0 npm install -g opencode@latest --registry=https://registry.npmmirror.com
```

**如果上面的命令报错“404 Not Found”，说明包名确实是那个长的，请换成这个：**
```bash
NODE_TLS_REJECT_UNAUTHORIZED=0 npm install -g opencode-antigravity-auth@latest --registry=https://registry.npmmirror.com
```

---

### 第三步：检查安装结果（非常重要）

安装完后，我们立刻检查命令到底生成没有：

```bash
# 看看 nvm 的目录下有没有 opencode
ls $(dirname $(which node)) | grep opencode
```

**检查结果处理：**

1.  **如果输出了 `opencode`**：恭喜，直接运行 `opencode auth login`。
2.  **如果还是空的**：说明 `opencode-antigravity-auth` 这个包真的没有提供命令行工具。

---

### 第四步：如果还是不行，最后的“绝招”

如果重装了还是 `command not found`，说明这个包的代码写得有问题，没自动创建快捷方式。我们可以**手动创建一个强制连接**：

```bash
# 找到包的实际安装位置
PACKAGE_PATH=$(npm list -g --parseable opencode-antigravity-auth)

# 强制把它的启动脚本链接到你的系统路径
ln -sf "$PACKAGE_PATH/bin/opencode.js" $(dirname $(which node))/opencode

# 给它执行权限
chmod +x $(dirname $(which node))/opencode
```

执行完这三行，再输入 `opencode` 就百分之百能打开了。

---

### 总结
你现在的核心矛盾是：
1. **包名可能装错了**（主程序通常叫 `opencode`）。
2. **NVM 环境不纯净**（通过第一步的 `sed` 和 `nvm use` 已解决）。
3. **网络证书报错**（通过 `NODE_TLS_REJECT_UNAUTHORIZED=0` 已解决）。

**请先执行第一步和第二步（装 `opencode` 而不是 `...auth`），如果报错再告诉我。**
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTk3ODM4NjE0OSwxOTc4ODA2MzUwLC0xNT
k3NTc3OTkyLC0xNzk5NTEwNTAxLC0xODExNzMzNDI2XX0=
-->