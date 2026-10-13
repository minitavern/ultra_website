# ultra_website（GitHub Pages 产物仓）

本目录只放从 `tavernmm/website` 同步过来的**静态官网**，以及本文件旁的 `publish.sh`。源码不在本仓。

线上：https://minitavern.github.io/ultra_website/

GitHub 仓：[minitavern/ultra_website](https://github.com/minitavern/ultra_website)

```text
work/
  tavernmm/website/   ← 源码（Vite 构建）
  ultra_website/      ← 本仓：静态产物 + 发布脚本 → 推 GitHub Pages
```

## 仓库连接

发布走 **SSH + minitavern 专用密钥**，不要用个人 GitHub 账号或 HTTPS 往本仓推。

### 远程

| 远程名 | 用途 | URL |
|--------|------|-----|
| `origin` | **发布目标**（GitHub Pages） | `git@github.com-minitavern:minitavern/ultra_website.git` |
| `gitlab` | 内网镜像（`publish.sh` 不会推这里） | `https://git.xman88.com/minitavern_ultra/minitavern_ultra_website.git` |

当前分支：`gh-pages`（跟踪 `origin/gh-pages`）。

查看连接：

```bash
git remote -v
git branch -vv
```

### SSH（本机一次性）

1. 用 **minitavern** 账号的密钥（不要用个人 `id_ed25519`）。公钥加到 [minitavern SSH keys](https://github.com/settings/keys)。
2. 在 `~/.ssh/config` 增加 Host 别名，让 Git 走专用密钥：

```
Host github.com-minitavern
        HostName github.com
        User git
        IdentityFile ~/.ssh/id_ed25519_minitavern
        IdentitiesOnly yes
```

3. 验证：

```bash
ssh -T git@github.com-minitavern
# 应出现：Hi minitavern! You've successfully authenticated...
```

`github.com-minitavern` 是 SSH Host 别名，不是真实域名；`REMOTE_URL` 必须写成 `git@github.com-minitavern:minitavern/ultra_website.git`。

### `.publish.env`

`publish.sh` 每次运行都会把 `origin` 写成这里的 `REMOTE_URL`。

```bash
cp .publish.env.example .publish.env
```

填写（文件已在 `.gitignore`，不要提交）：

```
GIT_NAME=minitavern
GIT_EMAIL=minitavern的邮箱或noreply@users.noreply.github.com
REMOTE_URL=git@github.com-minitavern:minitavern/ultra_website.git
```

若本机没有 SSH Host 别名，可用默认：

```
REMOTE_URL=git@github.com:minitavern/ultra_website.git
```

### 手动补远程（仓已存在但 remote 丢失时）

```bash
git remote add origin git@github.com-minitavern:minitavern/ultra_website.git
# 可选：内网镜像
git remote add gitlab https://git.xman88.com/minitavern_ultra/minitavern_ultra_website.git
git fetch origin
git checkout gh-pages
git branch -u origin/gh-pages
```

## 发布到 GitHub Pages

GitHub Pages 读的是 **`gh-pages` 分支根目录**，不是 `main`。`publish.sh` 会：切到 `gh-pages` → `git add` → 以 `GIT_NAME` / `GIT_EMAIL` 提交 → `git push -u origin gh-pages`。

### 首次

1. 在 GitHub 用 **minitavern** 账号创建公开空仓 [minitavern/ultra_website](https://github.com/minitavern/ultra_website)（不要初始化 README）。
2. 本目录：`cp .publish.env.example .publish.env`，填 `GIT_EMAIL` 和上面的 `REMOTE_URL`。
3. 源码仓构建并同步产物：

   ```bash
   cd ../tavernmm/website
   npm run sync
   ```

4. 本目录推送到 GitHub：

   ```bash
   cd ../../ultra_website
   ./publish.sh
   ```

5. GitHub 仓库 **Settings → Pages**：
   - Source：Deploy from a branch
   - Branch：`gh-pages` / `/ (root)`
   - Save 后约 1 分钟可打开 https://minitavern.github.io/ultra_website/

### 日常

先同步静态产物，再发布：

```bash
cd ../tavernmm/website
npm run sync

cd ../../ultra_website
./publish.sh
# 或自定义提交说明：
./publish.sh "更新官网首页"
```

源码仓一键（sync + publish）：

```bash
cd ../tavernmm/website
npm run publish:pages
```

无文件变更时 `publish.sh` 会跳过 commit，仍会 `git push`（确保远端已是最新）。

### 不要做的事

- 不要用个人邮箱 / 个人 GitHub 账号往本仓提交或推送
- 不要把 `tavernmm/website` 源码或主仓 `.git` 拷进本仓
- 不要用主仓 Actions 推本仓
- 不要提交 `.publish.env`
- 不要改 Pages 分支为 `main`（本仓发布分支是 `gh-pages`）
