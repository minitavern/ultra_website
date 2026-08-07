# ultra_website（GitHub Pages 产物仓）

本目录只放从 `tavernmm/website` 同步过来的**静态官网**，以及本文件旁的 `publish.sh`。

线上：https://minitavern.github.io/ultra_website/

## 首次

1. 在 GitHub 创建公开空仓 [minitavern/ultra_website](https://github.com/minitavern/ultra_website)
2. `cp .publish.env.example .publish.env` 并填写 `GIT_EMAIL`
3. 在 `tavernmm/website` 执行 `npm run sync`，再本目录 `./publish.sh`
4. 仓库 Settings → Pages → Branch `gh-pages` / root

## 日常

```bash
# 在 tavernmm/website
npm run sync
cd ../../ultra_website && ./publish.sh
```
