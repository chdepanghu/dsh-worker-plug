# 市场收录条目

这个目录放的是提交给 DSH 插件市场（[awesome-dsh-plugin](https://github.com/awesome-dsh-plugin/awesome-dsh-plugin)）的收录条目，**不是**运行时文件。

## 提交方法

1. 先确认仓库已满 1 天、已加 `dsh-plugin` topic（见 [`../docs/PUBLISHING.md`](../docs/PUBLISHING.md)）。
2. Fork `awesome-dsh-plugin/awesome-dsh-plugin`。
3. 把本目录的 [`chdepanghu__dsh-worker-plug.yml`](./chdepanghu__dsh-worker-plug.yml) 复制到对方仓库的 `data/plugins/` 下，文件名保持一致。
4. 只提交这一个文件，开 PR；PR 描述里写明用途、本机安装与打开结果、验证过的 Desktop 版本和平台。
5. CI 通过后由维护者人工阅读源码；合并后市场目录更新，插件出现在「插件市场」中。

条目字段说明（详见 awesome-dsh-plugin 的 `contributing.md`）：

| 字段 | 说明 |
| --- | --- |
| `url` | 必须与仓库地址完全一致 |
| `name` | 列表里显示的链接文字 |
| `category` | 23 个分类之一；本工作台取 `workflow` |
| `description.en` | 必填，一句话、以句号结尾、属实、不带营销词 |
| `description.zh` | 可选，维护者会补；这里已写好 |
| `icon` | 可选，GitHub 托管的 png/jpg/webp；不写则回退到作者头像 |

> 截图是**可选**的：作者仓库根目录放 `screenshots.json`（1–8 张相对路径图片）即可，市场会展示 AppStore 式截图；不写则从 README 自动抽取。本仓库暂未放截图，避免用示例数据冒充真机画面。