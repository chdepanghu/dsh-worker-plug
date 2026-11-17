# 发布与插件市场上架

本仓库是 **AI Native 工作台（`dsh-requirement-dev-workbench`）的插件市场分发仓库**：

- 开发主仓库：<https://github.com/chdepanghu/dsh-worker>（不改动）
- 分发仓库：<https://github.com/chdepanghu/dsh-worker-plug>（本仓库，可直接安装的 DSH bundle）

目标是让 DSH Desktop 的「插件市场」能一键安装。下面按官方收录规则（[awesome-dsh-plugin](https://github.com/awesome-dsh-plugin/awesome-dsh-plugin)）说明本仓库已经满足什么、还差哪几步。

---

## 1. 市场怎么装（机制）

- DSH 的插件市场（[dsh-market](https://github.com/dsh-market/dsh-market)）**只从策展清单安装**：清单数据源是 GitHub 仓库 `awesome-dsh-plugin/awesome-dsh-plugin` 的 `data/plugins/*.yml`。
- 安装来源优先级：**npm 包 → GitHub Release 的预构建 tarball → GitHub 源码**。市场会锁定到具体版本或 commit，校验失败就报错，不会自动换来源。
- 所以「进入市场」= 你的 GitHub 仓库 + 一个收录 YAML + 合并后的目录更新；「能直接安装」= 仓库根目录有可安装的 `package.json` / 入口 / bundle patch。

## 2. 本仓库已满足的收录条件

| 要求 | 本仓库现状 |
| --- | --- |
| 公开 GitHub 仓库 + 许可证 | 公开，MIT（[`LICENSE`](../LICENSE)） |
| 声明 `dsh.bundle` | [`package.json`](../package.json) → `dsh.bundle.patch: ./cordis.patch.yml` |
| `cordis.patch.yml` 里的 `name` 是包名 | `dsh-requirement-dev-workbench` |
| 真实可用代码 | `dsh/index.js`（Host，约 130 KB）+ `lib/client.js`（Client，约 275 KB）+ `test/` 自测 |
| 浏览器 UI 入口 | `exports["./client"]`、`dsh.client.platform: web`；官方 Harness 注册原生 `main` 主面板 + `sidebar.panellist` 左栏入口，DSH Desktop 检测到工作台宿主时仍走它 |
| 包体积 ≤ 8 MiB | 打出来约 0.5 MB（远低于上限） |
| 依赖指向原作者 | 无运行时依赖；只有官方 `@deepseek-ai/*` 的 optional peer |
| 不含密钥 / 本机路径 | 飞书表信息只存在本机 `test/smoke.local.json`（已 gitignore），仓库里没有 |

> 工作台市场（[dataelement/awesome-dsh-workbench](https://github.com/dataelement/awesome-dsh-workbench)）另有自己的条目格式；本仓库当前按**插件市场**准备。若还要上工作台市场，见第 6 节。

## 3. 还差的两步（需要作者本人操作）

### 3.1 仓库年龄满 1 天 + 加 topic

- 本仓库创建于 **2026-11-16**（UTC）；awesome-dsh-plugin 的 CI 要求仓库**创建满 1 天**，所以最早 **2026-11-17** 之后提交收录 PR 才会通过。
- 给仓库加 topic：**`dsh-plugin`**（GitHub → 仓库设置 → Topics）。CI 也会检查。

### 3.2 提交收录 PR

1. Fork `https://github.com/awesome-dsh-plugin/awesome-dsh-plugin`。
2. 把 [`awesome-list/chdepanghu__dsh-worker-plug.yml`](../awesome-list/chdepanghu__dsh-worker-plug.yml) 复制到对方的 `data/plugins/` 下（文件名保持不变）。
3. **只提交这一个文件**，开 PR；PR 描述里写明：
   - 用途：飞书需求/任务表 → AI 全流程（需求分析 / 开发 / 评审 / QA）→ 开发指令；
   - 本机验收：从本仓库安装后能打开「AI Native 工作台」、能读到表；
   - 验证过的宿主版本与平台（官方 DeepSeek Harness 0.2.2-rc.1 / DSH Desktop 0.12.0，Windows x64 均已实测）；
   - 外部依赖：本机 `lark-cli` 已登录（插件不保存飞书凭证）。
4. CI 检查（manifest、仓库年龄、格式、README 生成）通过后，维护者人工读源码；合并后市场目录更新，条目出现在「插件市场」中。

> 收录合并前，用户仍可通过 **设置 → 插件 → 添加插件** 填本仓库地址直接安装。

## 4. 可选：让安装更快更稳

### 4.1 npm 包

```sh
pnpm pack                      # 先核对包内容
npm publish --access public    # 首次需要 npm login
```

- `package.json` 的 `repository` 已指回本仓库（市场靠它把 npm 包与仓库关联）。
- 版本号与包名要和仓库一致；发版后市场会按 npm 版本提示更新。

### 4.2 GitHub Release 预构建 tarball（不发 npm 的替代）

```sh
pnpm pack
# 产出 dsh-requirement-dev-workbench-4.5.0.tgz
```

把 tgz 传到 GitHub Release（例如 tag `v4.5.0`），然后在收录 YAML 里加一行**固定 tag** 的 tarball：

```yaml
tarball: https://github.com/chdepanghu/dsh-worker-plug/releases/download/v4.5.0/dsh-requirement-dev-workbench-4.5.0.tgz
```

> 用 `releases/latest/download/<文件名>` 时文件名**不能带版本号**，否则下次发版会 404；固定 tag 则可以带版本号。必须是 GitHub 托管的 `https` `.tgz`。

## 5. 本地自检

```sh
node --check dsh/index.js
node --check lib/client.js
node test/smoke.mjs        # PASS / WARN / FAIL，默认无 FAIL
pnpm pack                  # 检查 tgz 体积 < 8 MiB
```

也可以在本机把这一个仓库目录（或 `pnpm pack` 产出的 `.tgz`）填进 **官方 DeepSeek Harness** 的「设置 → 插件 → 添加插件」，走一遍真实安装与打开；DSH Desktop 同样可用。

## 6. 工作台市场（可选）

DSH Desktop 另有一个「工作台市场」，收录仓库是 [dataelement/awesome-dsh-workbench](https://github.com/dataelement/awesome-dsh-workbench)，条目格式为 `data/workbenches/<owner>__<repo>.yml`，要求**仓库根目录只放一个工作台**、`dsh.client.inject` 含 `dsh-desktop-workbenches`、`exports["./client"]` 真实存在、带 1–5 张真机截图、中英文简介。

本仓库的包结构已满足这些硬性契约；要做的话，按该仓库 `catalog/README.md` 的 schema 另写一个条目，并补真机截图（在最终安装版本上打开 Desktop 实拍）。

## 7. 升级版本

1. 改 `package.json` 的 `version`、`lib/client.js` 的 `WORKBENCH_VERSION`、`CHANGELOG.md` 顶部三处保持一致（冒烟自测会检查）；
2. `node test/smoke.mjs`；
3. 提交并推送；
4. 走 npm 的：`npm publish` 新版本；走 Release 的：新建 Release 并上传同名固定文件名；
5. 源码安装来源会在下一次市场构建时被重新发现；条目本身通常不用改。每个版本仍由作者自己验证。