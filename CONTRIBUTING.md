# 如何向麦麦（MaiBot）插件中心贡献插件

感谢您愿意为麦麦（MaiBot）生态贡献插件。本指南说明当前推荐的插件提交流程，以及插件仓库需要满足的 `_manifest.json` v2 规范。

## 准备工作：插件仓库规范

在提交插件之前，请确保您的插件仓库满足以下要求：

### 必需文件

| 文件 | 要求 |
|------|------|
| `_manifest.json` | 位于插件仓库根目录，使用 manifest v2 结构 |
| `plugin.py` | 插件入口文件 |
| `LICENSE` | 许可证类型应与 `_manifest.json` 中的 `license` 字段一致 |
| `README.md` | 建议包含功能介绍、安装方式、配置说明和使用示例 |

### `_manifest.json` v2 规范

当前新插件应使用 `manifest_version: 2`。下面是一个最小可用示例：

```json
{
  "manifest_version": 2,
  "id": "github.username.my-plugin",
  "version": "1.0.0",
  "name": "示例插件",
  "description": "这是一个示例插件",
  "author": {
    "name": "作者名",
    "url": "https://github.com/username"
  },
  "license": "MIT",
  "urls": {
    "repository": "https://github.com/username/my-plugin",
    "homepage": "https://github.com/username/my-plugin",
    "documentation": "https://github.com/username/my-plugin/blob/main/README.md",
    "issues": "https://github.com/username/my-plugin/issues"
  },
  "host_application": {
    "min_version": "1.0.0",
    "max_version": "1.99.99"
  },
  "sdk": {
    "min_version": "2.0.0",
    "max_version": "2.99.99"
  },
  "dependencies": [],
  "capabilities": [],
  "i18n": {
    "default_locale": "zh-CN",
    "supported_locales": ["zh-CN"]
  }
}
```

**必需字段**：`manifest_version`, `id`, `version`, `name`, `description`, `author`, `license`, `urls`, `host_application`, `sdk`, `capabilities`, `i18n`

> [!IMPORTANT]
> - `manifest_version` 必须为 `2`。
> - `id` 应使用稳定、唯一的插件 ID，例如 `github.username.my-plugin`，不要使用空格或路径字符。
> - `version`、`host_application.min_version`、`host_application.max_version`、`sdk.min_version`、`sdk.max_version` 都应使用三段式版本号，例如 `1.0.0`。
> - `author` 必须是包含 `name` 和 `url` 的对象，不能是字符串。
> - `urls.repository` 必须是公开 GitHub 仓库的 HTTPS 地址。
> - `capabilities` 只声明插件实际需要的能力；没有额外能力时填写空数组。

> [!NOTE]
> `categories`、`keywords`、`repository_url`、`homepage_url` 等旧字段属于早期 manifest 或展示侧元数据，不属于当前 Host 严格校验的 manifest v2 字段。新插件不要把这些字段写入 `_manifest.json`，否则可能无法被新版 MaiBot 加载。

详细文档：[_manifest.json 字段说明](https://docs.mai-mai.org/develop/plugin-dev/manifest)

---

## 提交方式一：Issue 提交（推荐）

这是当前推荐方式，无需 Fork、无需本地 Git 操作，也能避免多人同时修改 `plugins.json` 带来的合并冲突。

### 步骤

1. **创建 Issue**：点击 [New Issue](../../issues/new/choose)，选择 **"Add Plugin / 添加插件"** 模板。
2. **填写信息**：
   - **插件 ID**：建议与 `_manifest.json` 中的 `id` 保持一致。
   - **仓库地址**：填写完整的公开 GitHub HTTPS URL，例如 `https://github.com/username/my-plugin`。
3. **等待验证**：CI 会自动读取插件仓库根目录的 `_manifest.json` 并进行校验，结果会评论在 Issue 中。
4. **等待批准**：验证通过后，维护者会审核并使用 `/approve` 批准。

### 状态标签说明

| 标签 | 含义 |
|------|------|
| `pending-validation` | 等待自动验证 |
| `validated` | 验证通过，等待维护者批准 |
| `validation-failed` | 验证失败，请根据提示修复 |
| `approved` | 已批准并添加到插件中心 |
| `rejected` | 被维护者拒绝 |

### 可用命令

| 命令 | 谁可以使用 | 说明 |
|------|-----------|------|
| `/recheck` | Issue 作者、维护者 | 重新触发验证，适合修改 manifest 后使用 |
| `/approve` | 仅维护者 | 批准并添加插件 |
| `/reject 原因` | 仅维护者 | 拒绝插件并说明原因 |

### 验证失败怎么办？

1. 根据 Issue 中的错误提示修改插件仓库。
2. 修改完成后，在 Issue 中评论 `/recheck`。
3. CI 会重新验证，结果会再次评论在 Issue 中。

---

## 提交方式二：PR 提交（已弃用）

> [!WARNING]
> PR 方式已弃用，建议使用 Issue 方式提交。
>
> PR 方式需要 Fork 仓库并手动修改 `plugins.json`，多人同时提交时容易产生合并冲突。

如果您仍希望使用 PR 方式：

1. **Fork 本仓库**
2. **Clone 并创建分支**：
   ```bash
   git clone https://github.com/YOUR-USERNAME/plugin-repo.git
   cd plugin-repo
   git checkout -b add/your-plugin-name
   ```
3. **编辑 `plugins.json`**，在数组末尾添加：
   ```json
   {
     "id": "github.username.my-plugin",
     "repositoryUrl": "https://github.com/username/my-plugin"
   }
   ```
4. **提交并推送**：
   ```bash
   git add plugins.json
   git commit -m "feat: add my plugin"
   git push origin add/your-plugin-name
   ```
5. **创建 Pull Request**

> [!NOTE]
> PR 创建后会收到引导消息，建议改用 Issue 方式提交。

---

## 常见问题

### 验证错误：无法获取 `_manifest.json`

可能原因：

- 文件名错误，必须是 `_manifest.json`。
- 插件仓库不是公开仓库。
- `_manifest.json` 不在 main/master/dev/develop 分支的根目录。

### 验证错误：`manifest_version` 不正确

新插件必须使用：

```json
"manifest_version": 2
```

### 验证错误：`author` 字段格式错误

正确格式：

```json
"author": {
  "name": "作者名",
  "url": "https://github.com/username"
}
```

错误格式：

```json
"author": "作者名"
```

### 插件 ID 格式要求

- 推荐：`github.username.my-plugin`
- 可以：`username.my-plugin`
- 不要使用：`My Plugin`
- 不要使用：`../path`

### 仓库 URL 格式要求

- 推荐：`https://github.com/username/repo-name`
- 不要使用：`https://github.com/username/repo-name.git`
- 不要使用：`git@github.com:username/repo-name.git`

---

## 需要帮助？

如果您在提交过程中遇到问题，可以在 Issue 中描述，我们会尽快回复。

感谢您对麦麦（MaiBot）生态的贡献。
