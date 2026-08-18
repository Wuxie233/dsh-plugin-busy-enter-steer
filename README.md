# dsh-plugin-busy-enter-steer

DSH host 守护插件：用户还没选过繁忙态 Enter 行为时，把 `ui-conversation.busyEnter` 写成 **插话发送（steer）**。

本体默认仍是排队发送。本插件不改 DSH 源码，只在本机用户设置里补一条显式覆盖。已经选过排队或插话的用户不受影响。

## 行为

- 触发：`ui-conversation` 命名空间注册后立刻写一次；之后该分节的 `settings/document-updated` 再检查。
- **写入**：用户分节没有 `busyEnter` 键时，写入 `steer`。
- **不写**：用户已经存了 `queue` 或 `steer`。
- 发送按钮、空闲态 Enter、已寻址 subagent 继续走本体 Queue。

## 安装 / 更新

```sh
./install.sh
# ~/.dsh/profiles/web/cordis.patch.yml:
#   - insert:
#       - id: busy-enter-steer
#         name: dsh-plugin-busy-enter-steer
# 重启 dsh web（host 半）
```

## 验证

- 部署后：`cd ~/.dsh/profiles/web && node -e "await import('dsh-plugin-busy-enter-steer')"`
- 纯函数：`node scripts/should-seed.test.mjs`
- 生效：`~/.dsh/settings.yaml` 出现 `ui-conversation.busyEnter: steer`；设置页「繁忙时 Enter 键行为」显示「插话发送」
