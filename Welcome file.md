
这就奇怪了：`npm` 显示安装成功了，但在你定义的全局目录下却找不到文件。这通常是因为 `nvm` 和 `npm prefix` 冲突导致安装路径“飘”了。

我们不猜路径了，直接用下面这几个命令排查并强制修复：

### 1. 先试试 `npx` (最快绕过路径问题的办法)
`npx` 会直接从你的 node_modules 里寻找可执行文件，不管它在不在 PATH 里。
请执行：
```bash
npx opencode-antigravity-auth auth login
```
或者试试短命令：
```bash
npx opencode auth login
```
**如果这两条命令能运行，说明程序就在你电脑里，只是快捷方式没挂对。**

---

### 2. 彻底清理并强制安装到 NVM 路径（终极解决方案）
由于你的 `nvm` 和全局 `prefix` 冲突，最好的办法是**取消自定义全局路径**，让 `nvm` 统一管理。请按顺序执行以下操作：

**第一步：删掉冲突的配置**
```bash
# 删除 npmrc 里的自定义路径配置
sed -i '' '/prefix=/d' ~/.npmrc

# 运行 nvm 提供的修复命令
nvm use --delete-prefix v20.19.0
```

**第二步：重新安装**
这时候 npm 会把工具装到 nvm 当前版本的目录下（这是最推荐的做法）：
```bash
npm install -g opencode-antigravity-auth@latest
```

**第三步：确认安装位置**
安装完后，执行：
```bash
which opencode
```
如果返回了类似 `/Users/qh2/.nvm/versions/node/v20.19.0/bin/opencode` 的路径，那就彻底成功了！

---

### 3. 如果 `which opencode` 还是没结果
这说明这个包提供的命令名称可能**不叫** `opencode`。我们搜一下它到底叫什么：
```bash
ls $(npm config get prefix)/bin | grep opencode
```
或者：
```bash
ls $(dirname $(which node)) | grep opencode
```

**请告诉我上面这些命令的输出结果，我帮你定准那个“消失”的命令叫什么。**
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE4NjUxNjE5ODcsLTE1OTc1Nzc5OTIsLT
E3OTk1MTA1MDEsLTE4MTE3MzM0MjZdfQ==
-->