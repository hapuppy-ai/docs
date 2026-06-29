# 双语(中 / 英)国际化 — 设计文档

- **日期**：2026-06-29
- **状态**：已批准设计，待写实施计划
- **目标站点**：Hapuppy 文档站(Mintlify，push 到 `main` 自动部署)

## 1. 背景与目标

当前站点为纯英文单语：`docs.json` 无 `languages` 字段，所有 `.mdx` 内容为英文，目录按内容类型(`guides/`、`api-reference/`)组织。

目标：启用 Mintlify 原生国际化，提供**英文(默认)+ 简体中文**双语，header 出现语言切换器，两套内容路由独立、结构对称。

## 2. 已确认决策

| 决策点 | 结论 |
|---|---|
| 默认语言 | **英文**(`languages[]` 首项、`default: true`) |
| 第二语言 | 简体中文 |
| 语言代码 | **`zh`**(URL 前缀 `/zh/`) |
| 中文内容来源 | 人工逐页翻译，进 git、完全可控 |
| 购买链接锚文本 | **"积分包(Credits Packs)"**(中文本地化 + 括注产品术语) |
| 产出顺序 | 先 Guides 8 页 → 再 API Reference 10 页 |

## 3. 非目标(明确不做)

- **不做按地区/浏览器语言自动适配重定向**。Mintlify 无内置该能力，仅提供手动语言切换器;站点为 Mintlify 托管，边缘重定向规则不可控。默认语言(英文)对所有首访者生效。
- **不做繁体中文**(如将来需要，再用 `zh-Hant` 另开)。
- **不翻译隐藏页**:`api-reference/videos/*`(5 页)与 `guides/errors.mdx` 当前未挂导航、源文件保留;中文侧保持同样的隐藏与缺席状态,与英文侧对称。
- **不改动英文页**内容/位置(纯新增 `zh/`)。

## 4. 改造策略

采用 **最小改动 + Mintlify 原生 i18n**：

- 英文页(默认语言)**保持现状、留在根目录**,无需迁移。
- 新增 `zh/` 镜像目录存放中文页。
- `docs.json` 将现有 `navigation.tabs` 包进 `navigation.languages[]`。

已排除的方案：

- **对称迁移**(英文也移入 `en/`)：需移动全部文件并重写所有内部链接,工作量与风险大,无实质收益。
- **Mintlify 后台机翻**：译文不进 git、不可逐字管控,与"人工逐页翻译"的决策冲突。

## 5. 目录结构

英文不动,新增 `zh/` 完整镜像：

```
index.mdx                      zh/index.mdx
quickstart.mdx                 zh/quickstart.mdx
guides/authentication.mdx      zh/guides/authentication.mdx
guides/credits.mdx             zh/guides/credits.mdx
guides/integrations.mdx        zh/guides/integrations.mdx
guides/verify-account.mdx      zh/guides/verify-account.mdx
guides/claude-apply.mdx        zh/guides/claude-apply.mdx
guides/faq.mdx                 zh/guides/faq.mdx
api-reference/chat/completions.mdx            zh/api-reference/chat/completions.mdx
api-reference/embeddings/create.mdx           zh/api-reference/embeddings/create.mdx
api-reference/images/generations.mdx          zh/api-reference/images/generations.mdx
api-reference/images/edits.mdx                zh/api-reference/images/edits.mdx
api-reference/audio/speech.mdx                zh/api-reference/audio/speech.mdx
api-reference/audio/transcriptions.mdx        zh/api-reference/audio/transcriptions.mdx
api-reference/audio/translations.mdx          zh/api-reference/audio/translations.mdx
api-reference/moderations/create.mdx          zh/api-reference/moderations/create.mdx
api-reference/models/list.mdx                 zh/api-reference/models/list.mdx
api-reference/models/get.mdx                  zh/api-reference/models/get.mdx
```

共 18 个页面需翻译。

## 6. `docs.json` 设计(完整蓝本)

`navbar`/`footer` 留在顶层全局共享;中文侧在 `languages[zh].navbar` 覆盖文案。改造后的完整文件：

```json
{
  "$schema": "https://mintlify.com/docs.json",
  "theme": "mint",
  "name": "Hapuppy",
  "colors": { "primary": "#8B5CF6", "light": "#A78BFA", "dark": "#7C3AED" },
  "favicon": "/favicon.svg",
  "logo": { "light": "/logo/light.svg", "dark": "/logo/dark.svg" },
  "navigation": {
    "languages": [
      {
        "language": "en",
        "default": true,
        "tabs": [
          {
            "tab": "Guides",
            "groups": [
              { "group": "Getting Started", "pages": ["index", "quickstart"] },
              { "group": "Core", "pages": ["guides/authentication", "guides/credits", "guides/integrations"] },
              { "group": "Account", "pages": ["guides/verify-account", "guides/claude-apply"] },
              { "group": "Support", "pages": ["guides/faq"] }
            ]
          },
          {
            "tab": "API Reference",
            "groups": [
              { "group": "Chat", "pages": ["api-reference/chat/completions"] },
              { "group": "Embeddings", "pages": ["api-reference/embeddings/create"] },
              { "group": "Images", "pages": ["api-reference/images/generations", "api-reference/images/edits"] },
              { "group": "Audio", "pages": ["api-reference/audio/speech", "api-reference/audio/transcriptions", "api-reference/audio/translations"] },
              { "group": "Moderations", "pages": ["api-reference/moderations/create"] },
              { "group": "Models", "pages": ["api-reference/models/list", "api-reference/models/get"] }
            ]
          }
        ]
      },
      {
        "language": "zh",
        "tabs": [
          {
            "tab": "指南",
            "groups": [
              { "group": "快速开始", "pages": ["zh/index", "zh/quickstart"] },
              { "group": "核心", "pages": ["zh/guides/authentication", "zh/guides/credits", "zh/guides/integrations"] },
              { "group": "账户", "pages": ["zh/guides/verify-account", "zh/guides/claude-apply"] },
              { "group": "支持", "pages": ["zh/guides/faq"] }
            ]
          },
          {
            "tab": "API 参考",
            "groups": [
              { "group": "对话", "pages": ["zh/api-reference/chat/completions"] },
              { "group": "嵌入", "pages": ["zh/api-reference/embeddings/create"] },
              { "group": "图像", "pages": ["zh/api-reference/images/generations", "zh/api-reference/images/edits"] },
              { "group": "音频", "pages": ["zh/api-reference/audio/speech", "zh/api-reference/audio/transcriptions", "zh/api-reference/audio/translations"] },
              { "group": "审核", "pages": ["zh/api-reference/moderations/create"] },
              { "group": "模型", "pages": ["zh/api-reference/models/list", "zh/api-reference/models/get"] }
            ]
          }
        ],
        "navbar": {
          "links": [ { "label": "支持", "href": "mailto:support@hapuppy.com" } ],
          "primary": { "type": "button", "label": "控制台", "href": "https://hapuppy.com/en/dashboard" }
        }
      }
    ]
  },
  "navbar": {
    "links": [ { "label": "Support", "href": "mailto:support@hapuppy.com" } ],
    "primary": { "type": "button", "label": "Dashboard", "href": "https://hapuppy.com/en/dashboard" }
  },
  "footer": { "socials": { "discord": "https://discord.gg/YWayJmZKCu" } }
}
```

## 7. 翻译范围

**翻译(18 页)**：见第 5 节清单。

**不翻译 / 保持隐藏**：

- `api-reference/videos/create|list|get|download|delete.mdx`
- `guides/errors.mdx`

这些英文源文件保留、不挂导航;中文侧不创建对应文件。将来任一隐藏页重新启用时,需同时补中文页并在 `languages[]` 两侧挂载。

## 8. 单页翻译规则

**译**：

- frontmatter 的 `title`、`sidebarTitle`、`description`。
- 正文段落与各级标题。
- `<Card>` 的标题与描述文字、`<Steps>`/`<Step>` 文案、表格的 Description/说明列、`<Note>`/`<Info>`/`<Warning>`/`<Accordion>` 文案。

**保留不动**：

- 整个代码块内容(`base_url`、模型名如 `claude-sonnet-4-20250514`、`sk-hapuppy-xxx`、JSON 响应、SSE 片段、cURL)。
- 代码栅栏的语言标签(` ```python Python ` 等)。
- MDX 组件标签与属性(`icon`、`cols`、`href`、`type`)。
- 请求参数表中的参数名、类型、枚举值(`model`、`string`、`auto`、`stream`)——仅译 Description 列。

**链接改写**：

- 中文页**内部**链接加 `/zh/` 前缀：`/quickstart` → `/zh/quickstart`，`/guides/authentication` → `/zh/guides/authentication`，`/api-reference/...` → `/zh/api-reference/...`。
- 页内锚点保留：`/guides/integrations#claude-code` → `/zh/guides/integrations#claude-code`。
- **外部**链接**不变**：`https://beta.hapuppy.com/v1`、`https://hapuppy.com/en/dashboard`、`https://hapuppy.com/en/dashboard/billing`、`https://hapuppy.com/en/dashboard/models`、`https://discord.gg/YWayJmZKCu`、`mailto:support@hapuppy.com`。

## 9. 术语与锚文本约定(全程遵循 CLAUDE.md)

- **品牌名 Hapuppy** 不译。
- **支持的 provider 短名单**仅：Claude、GPT、Gemini、DeepSeek、Llama、GLM(不得出现 Grok/xAI、Mistral/Mixtral、视频模型)。
- **API base URL** 一律 `https://beta.hapuppy.com/v1`(不得引入 `api.hapuppy.com`)。
- **定价**：一次性购买、非订阅;Lite $9.99 / Standard $19.99 / Pro $36.99;购买的积分进 vault、永不过期;Free 套餐 100K/天、每日重置、不滚存;全套餐 100 RPM;免费额度每日重置时间为北京时间午夜(UTC 16:00);支付经由 Creem。
- **积分计费公式**(不得合并为单一 `total_tokens × 乘数`)：

  ```
  credits = prompt_tokens × input_multiplier + completion_tokens × output_multiplier
  ```

- **Claude 变体**：无前缀(`claude-opus-4-7` 等)经 Claude Apply 审核后免费;`hapuppy/` 前缀同模型但允许角色扮演、付费走 vault 高倍率;单次 Claude Apply 审核同时解锁两种;审核为人工、无邮件通知,需到申请页查看状态。
- **账户验证 = Discord 绑定**(非邮箱/短信/支付),官方服务器内 1:1 绑定、不可复用;未绑定即使免费套餐也会被拒。
- **购买链接锚文本**:中文页用 **"积分包(Credits Packs)"**(首次出现保留英文括注以对应产品 UI 按钮)。
- 外部目标:support `support@hapuppy.com`、Discord `https://discord.gg/YWayJmZKCu`。

## 10. 导航 / UI 文案对照表

| 英文 | 中文 |
|---|---|
| Tab: Guides | 指南 |
| Tab: API Reference | API 参考 |
| Getting Started | 快速开始 |
| Core | 核心 |
| Account | 账户 |
| Support(group) | 支持 |
| Chat | 对话 |
| Embeddings | 嵌入 |
| Images | 图像 |
| Audio | 音频 |
| Moderations | 审核 |
| Models | 模型 |
| Navbar link: Support | 支持 |
| Navbar button: Dashboard | 控制台 |

`footer` 的 Discord 社交链接为全局共享,无需翻译。

## 11. 验证

- `npx mintlify dev`：确认 header 出现语言切换器;英文/中文两套 tab 与 group 正常渲染;抽查页面与代码块。
- `npx mintlify broken-links`：全站断链检查,重点核对 `/zh/` 内部链接均指向已存在的中文页。

## 12. CLAUDE.md 更新

改造完成后,在 CLAUDE.md 增补 i18n 约定,防止后续维护破坏双语对称：

- 站点为中(`zh/`)英(根目录)双语,英文为默认语言。
- 导航在 `docs.json` 的 `navigation.languages[]` 下分语言维护;新增页面须**中英各一**并在两个语言块同步挂载。
- 中文页路径加 `zh/` 前缀;中文页内部链接须用 `/zh/` 前缀,外部链接不变。
- 隐藏页规则:启用时两侧同步补齐。
- 术语/锚文本以本设计文档第 9 节为准。

## 13. 风险与退路

- **`languages[]` 下嵌套 `tabs` 未见官方独立示例**(官方 i18n 示例均用 `groups`)。实施第一步即用最小切片(改 `docs.json` + 翻译 `zh/index.mdx`)跑 `mintlify dev` 验证 tab 结构是否渲染。
  - 退路 A：若 `languages[].tabs` 不被支持,降级为 `languages[].groups`(取消 Guides/API Reference 两 tab 的分隔,改为顶层并列分组)。
  - 退路 B：保留 tab 分隔但改用 `anchors`/`dropdowns` 组织。
- 验证以本地 `mintlify dev` 实际渲染为准,不臆断。

## 14. 产出顺序

1. **垂直切片(验证结构)**：改 `docs.json` 加 `languages`(en + zh) + 翻译 `zh/index.mdx` + `zh/quickstart.mdx` → `mintlify dev` 验证切换器、路由、tab、内链。结构确认后再继续。
2. **Guides 翻译**：`zh/guides/` 下 6 页(authentication、credits、integrations、verify-account、claude-apply、faq)。
3. **API Reference 翻译**：`zh/api-reference/` 下 10 页。
4. **收尾**：更新 CLAUDE.md;`mintlify broken-links` 全量验证。

各阶段独立提交(中文 commit message,如 `docs: 新增中文版首页与快速开始,启用双语导航`)。
