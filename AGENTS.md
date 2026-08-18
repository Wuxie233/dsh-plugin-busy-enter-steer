# busy-enter-steer

DSH host-only 守护插件：用户未覆盖 `ui-conversation.busyEnter` 时写入 `steer`。

## Architecture

- 单半插件（仅 host，无浏览器半、无 `dsh.client`）：`lib/index.js` 等 `ui-conversation` 注册后 `settings.mutate`。
- 判定在 `lib/logic.js` 的 `shouldSeedSteer`：只看用户分节有没有 `busyEnter` 键，不比较解析后的默认值。
- 本体产品默认仍是 Queue；本插件把本机未设置用户变成显式 Steer。

## Conventions

- 源码真身在本仓库；`install.sh` 复制部署到 `$DSH_HOME/profiles/node_modules/<包名>/`（绝不 symlink）。
- host 半改动必须重启 dsh web。
- 提交身份用命令级 env 覆盖，不写持久 git config。

## Gotchas & Decisions

- **不要改 DSH 源码默认值**：升级会冲掉；本插件只写用户文档。
- **不要在用户已选 Queue 后再写回 Steer**：监听 `settings/document-updated` 时必须先看用户分节是否已有该键。
- **不要用 symlink 部署**：Node ESM 会解析到仓库路径，然后找不到 `@deepseek-ai/dsh-settings`。
- **包名两处一致**：`package.json` `name` 与 `cordis.patch.yml` 挂载行 `name`。
- **Config 必须是 schemastery schema**，不能是普通函数。

## Commands

- `./install.sh` — 部署（幂等）
- `node scripts/should-seed.test.mjs` — 纯函数
- `node --check`：ESM 复制为 `.mjs` 再查
- 生效验证：`cd ~/.dsh/profiles/web && node -e "await import('dsh-plugin-busy-enter-steer')"`

## Module Map

单包。`lib/logic.js` + `lib/index.js`。参见 `README.md`。
