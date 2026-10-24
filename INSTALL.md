# dsh-purge full build 1.1.77 — 安装说明

这份包是 **DSH 0.2.1-rc.2** 用的 `dsh-purge` 完整版：

- 上游 1.1.77 主体
- 回补 1.1.71 的 23 个 `skills/redteam` 技能
- 回补“自己的服务器”白名单与保存表单
- 修了手动 tarball 安装不生成 bundle-root 行的问题

不支持 DSH 0.1.5。旧 web profile（0.1.5）装不上，请用 0.2.1 的 profile。

## 1. 确认环境

- DSH `0.2.1-rc.2`（桌面端或 web 端都可以，按 profile 隔离）
- Node.js >= 18
- pnpm（DSH profile 用 pnpm 管理依赖）

先关掉 DSH，避免文件被占用。

## 2. 备份 profile

以桌面端默认 profile 为例，PowerShell：

```powershell
$profileDir = "$env:USERPROFILE\.dsh\profiles\desktop"
Copy-Item "$profileDir\package.json" "$profileDir\package.json.bak"
Copy-Item "$profileDir\cordis.patch.yml" "$profileDir\cordis.patch.yml.bak" -ErrorAction SilentlyContinue
```

web 端把 `desktop` 换成实际的 profile 目录名。

## 3. 安装 tarball

把 `dsh-purge-1.1.77-full.tgz` 放到一个固定路径，例如 `C:\dsh\dsh-purge-1.1.77-full.tgz`，然后：

```powershell
cd "$env:USERPROFILE\.dsh\profiles\desktop"
pnpm add "file:C:/dsh/dsh-purge-1.1.77-full.tgz"
```

pnpm 可能会提示 `ERR_PNPM_IGNORED_BUILDS`。这是因为 pnpm 默认不跑依赖的
`postinstall`，**不影响使用**，下一步手动把插件加进 bundles 即可。

## 4. 手动把 dsh-purge 加入 bundles

打开 profile 的 `package.json`，在 `dsh.profile.bundles` 数组里加一项
`"dsh-purge"`。保留原有内容，不要整段替换。例如：

```json
{
  "dsh": {
    "profile": {
      "bundles": [
        "@deepseek-ai/dsh-base",
        "@deepseek-ai/dsh-web-app",
        "dsh-purge"
      ]
    }
  }
}
```

同时确认 `dependencies` 里有：

```json
"dsh-purge": "file:C:/dsh/dsh-purge-1.1.77-full.tgz"
```

如果之前装过别的 dsh-purge，先删掉旧的依赖和旧的 bundles 项，避免重复。

## 5. 重启 DSH

完全退出 DSH 再启动，不要只关窗口。桌面端可以右键任务栏图标退出，或在任务管理器结束
`DeepSeek Harness` 进程后重开。

## 6. 验证

1. 左侧栏点 **「插件」**（不是“设置 → 内置插件”）。
2. “已安装插件”里应出现 `dsh-purge`，并带启用开关。
3. 新建/打开会话，标题旁应出现 `dsh-purge` 侧栏按钮，里面有“清洗”和“演练台”。
4. 演练台里应能看到 23 个技能。

如果只看到红色演练台、没有清洗面板，通常是主插件行没加载：检查 profile 的
`package.json` 是否包含 `"dsh-purge"`，以及安装的是不是这份 `1.1.77-full.tgz`。

## 7. 开关与使用

- **开/关插件**：左侧「插件」页的开关。不需要去创造模式跟模型说，不费 token。
- **应用/还原补丁**：会话标题旁的 `dsh-purge` → “清洗”。
- **自己的服务器**：`dsh-purge` → “清洗”页底部的“自己的服务器”文本框，每行一个
  精确 IP 或完整主机名。只放行这里明确登记的地址；不支持网段、通配符、后缀匹配。
- 白名单文件位置：`%USERPROFILE%\.dsh\net-scope-allow.txt`。

## 8. 卸载

1. 从 profile 的 `package.json` 删除 `"dsh-purge"` 依赖和 bundles 项。
2. 在 profile 目录执行：

```powershell
pnpm remove dsh-purge
```

3. 重启 DSH。
4. `%USERPROFILE%\.dsh\net-scope-allow.txt` 可以保留，也可以手动删除。

## 9. 常见问题

**pnpm 报 `ERR_PNPM_IGNORED_BUILDS`**
正常。按第 4 步手动加 bundles。

**启动报 `already queued: dsh-purge`**
说明 bundles 里已经有一份，或安装方式与补丁冲突。检查 `package.json` 是否重复；
如果是用 codeload 安装而不是这份 tarball，可以删掉 `cordis.patch.yml` 里第二个
`- insert:` 段（本次补丁只针对手动 tarball 安装）。

**插件页没有开关**
确认打开的是左侧「插件」，不是“设置 → 内置插件”。后者是只读的 preset 组成列表。

**DSH 0.1.5 装不上**
正常。请升级到 0.2.1-rc.2，或使用单独的 0.2.1 profile。

## 10. 使用边界与署名

- 只对你拥有或已获得明确书面授权的目标使用。不要打第三方公网目标。
- 本包保留上游 Apache-2.0 `LICENSE` / `NOTICE`，技能来自 MIT 的 1.1.71 镜像。
- 上游作者 **YuJunZhiXue**，镜像 **mmparko**。保留署名，不要倒卖或用于收费分发。
- 详细修改说明见同目录 `PATCH-NOTICE.md`。
