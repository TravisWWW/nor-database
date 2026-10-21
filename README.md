# nor-database —— 图鉴数据专用仓库

这个仓库**只放图鉴数据**：不放任何网站代码，也不放任何 workflow。存在的唯一目的：

> 让「小伴侣」用一个**专属令牌**把各账号的图鉴记录传上来，而那个令牌**碰不到** `norr-wiki`
> 和其他任何项目 —— 因为细粒度令牌被限制在**本仓库**，且本仓库里只有 JSON。

## 数据流

```
小伴侣「同步到公共图鉴」
   │  PUT contrib/char-<角色ID>.json                                  ← 内置专属令牌，用户零配置
   │  POST norr-wiki .../workflows/bestiary-merge.yml/dispatches      ← 通知立刻合并
   ▼
TravisWWW/nor-database（本仓库，只有数据）
   ▼
TravisWWW/norr-wiki
   │  拉取本仓库 contrib/*.json → node tools/merge-bestiary-contrib.mjs
   ▼
src/data/bestiary-live.json（唯一一份权威图鉴文档）→ 构建 → GitHub Pages
```

norr-wiki 那边同时挂了 **每 5 分钟定时兜底**，所以即使通知丢了，数据也会在几分钟内被合并。

## 目录

- `contrib/` —— 每个账号一份 `char-<角色ID>.json`（小伴侣自动上传；同名覆盖，天然去重）。

## 为什么单独开一个仓库

GitHub 的细粒度令牌**不能按文件路径授权**，只能按仓库授权；而「按路径限制推送」的 push ruleset
只有 Team / Enterprise 才有。所以要让令牌「只能传图鉴数据」，唯一可靠的做法就是：
**把图鉴数据放进一个只有图鉴数据的仓库**，令牌只对这个仓库有 `Contents: Read and write`。
这样即使令牌公开，也改不了 wiki、改不了任何其他项目。

⚠️ 请**不要**为本仓库开启 GitHub Pages —— `traviswww.github.io` 是所有项目共用的同源域名。
