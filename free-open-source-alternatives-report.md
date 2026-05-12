# Knowledge Work Plugins：免费/开源平替深度调研报告

> 调研日期：2026-05-12  
> 调研范围：knowledge-work-plugins 仓库中 21 个插件涉及的 40+ 外部 MCP 服务与 Marketplace 第三方插件  
> 核心结论：**项目本身零依赖、全开源；但生态中的外部 SaaS 几乎全是国外收费服务。本报告为每个服务提供至少一条免费/开源/自托管替代路径。**

---

## 目录

1. [执行摘要](#1-执行摘要)
2. [总览矩阵：原版 vs 平替](#2-总览矩阵原版-vs-平替)
3. [深度分析按领域](#3-深度分析按领域)
   - 3.1 沟通协作（Slack / MS Teams）
   - 3.2 项目管理（Linear / Asana / Monday / ClickUp / Jira）
   - 3.3 文档与知识库（Notion / Confluence）
   - 3.4 设计（Figma / Canva）
   - 3.5 代码托管与研发（GitHub / GitLab）
   - 3.6 监控、运维与可观测性（Datadog / PagerDuty）
   - 3.7 CRM 与销售（HubSpot / Salesforce / Apollo / ZoomInfo）
   - 3.8 数据平台与 BI（BigQuery / Snowflake / Databricks / Hex）
   - 3.9 营销与 SEO（Ahrefs / SimilarWeb / Klaviyo）
   - 3.10 视频会议（Zoom）
   - 3.11 文件存储、法务与电子签名（Box / DocuSign / Egnyte）
   - 3.12 办公套件（Google Workspace / MS365）
   - 3.13 生物研究工具
   - 3.14 PDF 处理
4. [中国大陆可用性专项](#4-中国大陆可用性专项)
5. [自托管成本估算](#5-自托管成本估算)
6. [推荐组合方案](#6-推荐组合方案)
7. [结论与行动建议](#7-结论与行动建议)

---

## 1. 执行摘要

### 项目本身

knowledge-work-plugins 是 Anthropic 维护的**纯声明式插件仓库**，仅包含 Markdown + JSON，**无需任何运行时、零依赖、Apache 2.0 开源**。你可以在中国大陆任意下载、修改、分发这些插件文件。

### 外部依赖的真实面目

问题出在 `.mcp.json` 中定义的 **MCP 服务器**——这些是 Claude Code 连接外部工具的 HTTP 端点，对应的全是国际商业 SaaS：

| 风险维度 | 情况 |
|---------|------|
| **收费** | 90% 以上的 MCP 服务是商业付费产品（Linear $8/人/月、Datadog 按主机+流量计费、Salesforce $175/人/月…） |
| **闭源** | 所有 SaaS 服务端均为闭源；Marketplace 中的第三方插件也是外部仓库，源码不可控 |
| **翻墙** | 绝大多数服务端点（`.com`/`.io`/`.app`）在中国大陆需要代理才能访问 |
| **国内适用性** | 仅少数服务（如 Atlassian 部分功能、GitHub 基础 API）在无代理时偶发可用；大部分服务体验极差或无法访问 |

### 好消息

几乎每一个付费 SaaS 都有**免费层、开源替代品或可自托管方案**。对于"不花钱+数据自主+国内可用"的三重目标，最佳策略是：**用开源自托管工具替换 MCP 连接器，再让这些工具暴露 API 给 Claude Code 调用**。

---

## 2. 总览矩阵：原版 vs 平替

> 评分说明：⭐⭐⭐ = 极佳平替，功能/体验接近原版；⭐⭐ = 可用，有取舍；⭐ = 勉强可用或场景受限；`-` = 无直接平替  
> "国内直连"列指在中国大陆网络环境下不翻墙即可访问部署实例的能力。

| 类别 | 原版（收费/闭源） | 开源/自托管平替 | 免费商用平替 | 国内直连 | 平替推荐指数 |
|------|------------------|----------------|-------------|---------|------------|
| **沟通** | Slack | Mattermost, Rocket.Chat, Element | 钉钉, 飞书, 企业微信 | ✅ | ⭐⭐⭐ |
| **项目管理** | Linear | **Plane**, Focalboard, Taiga | GitHub Projects, 飞书项目 | ✅ | ⭐⭐⭐ |
| | Asana / Monday / ClickUp | Plane, Focalboard, OpenProject | 飞书多维表格, 钉钉项目 | ✅ | ⭐⭐⭐ |
| | Jira (Confluence) | OpenProject, Redmine | 阿里云效, 禅道 | ✅ | ⭐⭐ |
| **文档/Wiki** | Notion | **AFFiNE**, AppFlowy, Docmost, Outline | 语雀, 飞书文档, 石墨文档 | ✅ | ⭐⭐⭐ |
| | Confluence | Docmost, BookStack, XWiki, Wiki.js | 语雀, 飞书知识库 | ✅ | ⭐⭐⭐ |
| **设计** | Figma | **Penpot** | 即时设计 (js.design) | ✅ | ⭐⭐⭐ |
| | Canva | Penpot, GIMP+Scribus | 创客贴, 稿定设计 | ✅ | ⭐⭐ |
| **代码托管** | GitHub | **GitLab CE**, **Gitea**, **Forgejo** | Gitee, Coding | ✅ | ⭐⭐⭐ |
| | GitHub Copilot | Continue.dev + Ollama, Codeium | 通义灵码, 文心快码 | ✅ | ⭐⭐⭐ |
| **监控/APM** | Datadog | **SigNoz**, OpenObserve, Prometheus+Grafana | 阿里云 ARMS, 腾讯云 APM | ✅ | ⭐⭐⭐ |
| | PagerDuty | Keep, Prometheus AlertManager+日历 | 阿里云告警, 腾讯云监控 | ⚠️ 自托管可 | ⭐⭐ |
| **CRM** | HubSpot | **Twenty CRM**, EspoCRM, SuiteCRM | 销售易, 纷享销客 | ✅ | ⭐⭐⭐ |
| | Salesforce | SuiteCRM, ERPNext, Odoo CE | 销售易, 纷享销客 | ✅ | ⭐⭐⭐ |
| | Apollo/ZoomInfo | 无直接开源平替 | **企查查 API**, 天眼查 API | ✅ | ⭐⭐ |
| **数据仓库** | Snowflake | **Apache Doris**, **StarRocks**, ClickHouse | 阿里云 Hologres, 华为云 DWS | ✅ | ⭐⭐⭐ |
| | BigQuery | ClickHouse, Apache Druid, Trino | 阿里云 MaxCompute, 华为云 MRS | ✅ | ⭐⭐⭐ |
| | Databricks | Apache Spark (自托管), Trino | 阿里云 EMR, 华为云 MRS | ✅ | ⭐⭐ |
| **BI/分析** | Hex | **Metabase**, **Apache Superset**, JupyterHub | 阿里云 Quick BI, 网易有数 | ✅ | ⭐⭐⭐ |
| **SEO/营销** | Ahrefs | SerpBear (自托管), Google Search Console | 5118, 站长工具 | ✅ | ⭐⭐ |
| | SimilarWeb | Plausible Analytics (自己站点) | 无 | ⚠️ | ⭐ |
| | Klaviyo | **Mautic** (开源营销自动化) | 无 | ✅ | ⭐⭐⭐ |
| | Supermetrics | 自建 Airbyte + Metabase | 无 | ✅ | ⭐⭐ |
| **视频会议** | Zoom | **Jitsi Meet**, BigBlueButton | 腾讯会议, 飞书会议, 钉钉会议 | ✅ | ⭐⭐⭐ |
| **存储/网盘** | Box / Egnyte | **Nextcloud**, Seafile, ownCloud | 阿里云盘, 坚果云 | ✅ | ⭐⭐⭐ |
| **电子签名** | DocuSign | **DocuSeal**, **Documenso**, OpenSign | 法大大, e签宝 | ✅ | ⭐⭐⭐ |
| **办公套件** | Google Workspace / MS365 | **Nextcloud** (+OnlyOffice/Collabora), SOGo | 钉钉, 飞书, WPS+ | ✅ | ⭐⭐⭐ |
| **生物研究** | PubMed | PubMed 本身就是免费的！ | 知网, 万方, 维普 | ✅ | ⭐⭐⭐ |
| | BioRender | Bioicons + Inkscape | 无 | ✅ | ⭐ |
| | bioRxiv / ChEMBL | 本身就是免费学术资源 | 同上 | ⚠️ 需代理 | ⭐⭐ |
| **PDF** | MCP PDF Server | `@modelcontextprotocol/server-pdf` 本身就是开源 npm 包 | 无 | ✅ | ⭐⭐⭐ |

---

## 3. 深度分析（按领域）

### 3.1 沟通协作（Slack / MS Teams）

#### 原版痛点
- Slack：$7.25/人/月起，mcp.slack.com 在中国大陆访问困难
- MS Teams：microsoft365.mcp.claude.com 是 Anthropic 代理端点，稳定性未知

#### 开源自托管平替

**Mattermost**（⭐⭐⭐ 首推）
- 协议：MIT + 商业版
- 特性：Slack 的像素级替代品，线程、 reaction、Huddle、集成市场齐全
- 自托管：单 Docker 容器即可运行，支持 PostgreSQL/MySQL
- 与 Claude 集成：可通过 Mattermost 的 Bot API + 自定义 MCP HTTP 桥接
- 成本：$0 许可费，VPS $5-20/月

**Rocket.Chat**（⭐⭐⭐）
- 协议：MIT
- 特性：功能比 Mattermost 更全（含 Live Chat、Omnichannel），但 heavier
- 自托管：Docker Compose 一键部署

**Element (Matrix)**（⭐⭐⭐）
- 协议：Apache 2.0
- 特性：去中心化、端到端加密、联邦宇宙（Federation）
- 自托管：Synapse 服务端 + Element Web 客户端

#### 国内免费商用平替
- **钉钉**（阿里巴巴）：国内最普及，开放平台支持自定义机器人、审批流，可通过Webhook桥接Claude
- **飞书**（字节跳动）：文档+IM深度整合，开放平台和Bot API完善，国内团队体验最佳
- **企业微信**：适合已有微信生态的企业

#### 建议
| 场景 | 推荐方案 |
|------|---------|
| 技术团队+数据主权优先 | Mattermost 自托管 |
| 国内团队日常协作 | 飞书 |
| 强安全/去中心化需求 | Element/Matrix 自托管 |

---

### 3.2 项目管理（Linear / Asana / Monday / ClickUp / Jira）

#### 原版痛点
- Linear：$8/人/月，仅 Cloud，无自托管，mcp.linear.app 需翻墙
- Asana/Monday/ClickUp：$7-15/人/月，均为美国 SaaS
- Jira：功能臃肿、定价复杂、国内访问慢

#### 开源自托管平替

**Plane**（⭐⭐⭐ Linear 最佳平替）
- 协议：AGPL-3.0
- GitHub Stars：22,000+
- 特性：被称为"开源版 Linear"，Cycles（Sprint）、Modules（Epic）、多视图（Board/List/Gantt）、GitHub/GitLab 集成
- 自托管：Docker Compose，2C4G 服务器即可跑起
- 成本：$0 许可，云主机约 $20/月
- 注意：AGPL 协议要求修改后开源，仅供内部使用无影响

**Focalboard**（⭐⭐⭐ 轻量平替）
- 协议：Apache 2.0（原 Mattermost 团队开发）
- 特性：Trello/Notion/Asana 的交集，Board + 表格视图
- 自托管：单二进制文件或 Docker

**OpenProject**（⭐⭐⭐ Jira 深度平替）
- 协议：GPL-3.0
- 特性：甘特图、路线图、工时、成本、Scrum/Kanban、Wiki 全包
- 自托管：Docker 或包管理器安装

**Taiga**（⭐⭐）
- 协议：MPL-2.0
- 特性：曾是最美的开源项目管理工具，2024 年后维护放缓，社区版功能有所缩减

#### 国内免费商用平替
- **飞书项目**：字节跳动同款，工作流引擎强，国内直连，免费版够用
- **阿里云效（云效 Projex）**：DevOps + 项目管理一体，国内开发者友好
- **禅道**：国产开源项目管理（ZPL 协议），敏捷+瀑布全覆盖，国内社区活跃

#### 建议
| 原版 | 最佳平替 |
|------|---------|
| Linear | Plane |
| Asana/Monday/ClickUp | Focalboard 或 Plane |
| Jira | OpenProject 或禅道 |

---

### 3.3 文档与知识库（Notion / Confluence）

#### 原版痛点
- Notion：$8/人/月，数据在境外，mcp.notion.com 需翻墙
- Confluence：$5.75/人/月起，重量级，国内访问慢

#### 开源自托管平替

**Docmost**（⭐⭐⭐ Confluence/Notion 最佳平替）
- 协议：AGPL-3.0
- 特性：实时协作编辑器、Spaces、Drawio/Excalidraw/Mermaid 图表、评论、全文搜索、附件搜索（PDF/DOCX 内容索引）
- 认证：内置邮箱密码登录（比 Outline 友好得多）
- 自托管：Docker Compose，需 PostgreSQL + Redis
- 与 Claude 集成：可通过 MCP 连接器暴露知识库搜索 API

**AFFiNE**（⭐⭐⭐ Notion 现代平替）
- 协议：MIT（本地编辑器）
- 特性：Docs + Edgeless（白板）+ 数据库视图，Notion 的像素级竞争者
- 部署：本地优先，可选自托管服务端（PostgreSQL + Redis）
- 亮点：块级编辑、本地离线可用、GitHub 活跃度高

**Outline**（⭐⭐）
- 协议：BSL（Business Source License）——非严格开源，一年后转 Apache 2.0
- 特性：极美、极快的 Wiki，Slack 风格
- 坑点：无内置邮箱登录，必须配 OIDC/Google OAuth；BSL 协议对商业使用有时间限制

**BookStack**（⭐⭐⭐ 简单 Wiki）
- 协议：MIT
- 特性：书架→书→章节→页面的层级结构，非常适合技术文档
- 自托管：PHP + MySQL，5 分钟部署

**Wiki.js**（⭐⭐⭐ 全能 Wiki）
- 协议：AGPL-3.0
- 特性：支持 Markdown/WYSIWYG/Asciidoc 多编辑器、Git 同步、SSO、全文搜索
- 自托管：Node.js + PostgreSQL

#### 国内免费商用平替
- **语雀**（蚂蚁集团）：国内知识库体验最佳，免费版功能慷慨，支持团队空间
- **飞书文档**：与 IM 无缝衔接，多维表格强大
- **石墨文档**：协作体验好，适合中小型团队

#### 建议
| 原版 | 最佳平替 |
|------|---------|
| Notion | AFFiNE 或 Docmost（自托管）/ 语雀（国内 SaaS）|
| Confluence | Docmost 或 Wiki.js / BookStack |

---

### 3.4 设计（Figma / Canva）

#### 原版痛点
- Figma：$15/人/月起，被 Adobe 收购后涨价预期，mcp.figma.com 需翻墙
- Canva：$13/人/月起，设计资产云端存储

#### 开源自托管平替

**Penpot**（⭐⭐⭐ Figma 最佳开源平替）
- 协议：MPL-2.0（完全开源）
- GitHub Stars： growing rapidly，社区活跃
- 特性：
  - 基于 SVG/CSS/HTML 原生 Web 标准，设计师输出的就是前端可用的 Flex/Grid 代码
  - 实时多人协作、组件系统、原型交互、设计令牌（Tokens）
  - 免费自托管：无限文件、无限团队、无限编辑器
- 部署：Docker Compose，2C4G 起
- 与 Figma 的差异：插件生态较小、超大文件性能略逊、复杂动效支持有限
- 成本：$0 许可，服务器 $20/月；Cloud 版 $7/编辑器/月（上限 $175）

#### 国内免费商用平替
- **即时设计 (js.design)**：国产 Figma 平替，界面几乎一致，免费版 generous，服务器在国内
- **创客贴 / 稿定设计**：Canva 的国内替代品，模板丰富

#### 建议
| 原版 | 最佳平替 |
|------|---------|
| Figma | Penpot（自托管）或即时设计（国内 SaaS）|
| Canva | 创客贴 / 稿定设计 |

---

### 3.5 代码托管与研发（GitHub / GitLab）

#### 原版痛点
- GitHub Copilot MCP：`api.githubcopilot.com/mcp/` 需要 GitHub Copilot 订阅且翻墙

#### 开源自托管平替

**GitLab CE**（⭐⭐⭐）
- 协议：MIT（社区版）
- 特性：代码托管 + CI/CD + 项目管理 + 容器注册表，一站式 DevOps
- 自托管：Omnibus 包或 Docker，4C8G 推荐
- 注意：EE（企业版）功能收费，但 CE 已足够大部分团队

**Gitea**（⭐⭐⭐ 轻量首选）
- 协议：MIT
- 特性：GitHub 的极简替代品，资源占用极低（树莓派都能跑）
- 自托管：单二进制文件 + SQLite，5 分钟启动
- 包含：Issues、PR、Wiki、Actions（Gitea Actions 兼容 GitHub Actions）

**Forgejo**（⭐⭐⭐ Gitea 分支）
- 协议：MIT
- 特性：Gitea 的社区驱动分支，更激进的开源承诺，活跃维护

#### 国内免费商用平替
- **Gitee**（开源中国）：国内最大代码托管，免费私有仓库，Gitee AI 提供国产 Copilot
- **Coding**（腾讯云）：国内开发者常用，与腾讯云生态集成

#### AI 编程助手平替（替代 GitHub Copilot）
| 工具 | 类型 | 说明 |
|------|------|------|
| **Continue.dev** + Ollama | 开源自托管 | VS Code 插件，连接本地 LLM，零代码外泄 |
| **Codeium** | 免费商用 | 个人免费无限使用，国内可访问 |
| 通义灵码 | 国产免费 | 阿里云出品，国内体验最佳 |
| 文心快码 | 国产免费 | 百度出品 |

---

### 3.6 监控、运维与可观测性（Datadog / PagerDuty）

#### 原版痛点
- Datadog：$15/主机/月 + 流量费，账单 notoriously 不可预测，mcp.datadoghq.com 需翻墙
- PagerDuty：$21-41/人/月，仅 Cloud

#### 开源自托管平替

**SigNoz**（⭐⭐⭐ Datadog 最佳平替）
- 协议：Apache 2.0
- GitHub Stars：24,000+
- 特性：OpenTelemetry 原生，统一 Logs/Metrics/Traces，ClickHouse 后端高性能
- 部署：Docker 或 Kubernetes Helm
- 成本：$0 许可，云主机 $20-50/月；SigNoz Cloud $49/月起
- 注意：国内网络下 SigNoz Cloud 可能需代理，但自托管完全无此问题

**OpenObserve**（⭐⭐⭐）
- 协议：Apache 2.0
- 特性：号称比 Elasticsearch 降低 140 倍存储成本，支持 Logs/Metrics/Traces/RUM
- 部署：单二进制文件，极简

**Prometheus + Grafana + Loki + Tempo**（⭐⭐⭐ 经典组合）
- 协议：Apache 2.0
- 特性：CNCF 毕业项目，生态最成熟，社区最大
- 部署：成熟，文档极丰富

**Uptime Kuma**（⭐⭐⭐ 极简可用性监控）
- 协议：MIT
- 特性：花哨的监控面板，支持 HTTP/TCP/DNS/Push 监控，通知渠道 20+
- 部署：单 Docker 容器

#### PagerDuty 平替（⚠️ 注意：开源 On-Call 领域 2025-2026 有大震荡）

> **关键预警**：Grafana OnCall（曾是最强开源 PagerDuty 平替）已于 **2026-03-24 被 Grafana 归档**，停止维护。Netflix Dispatch 也于 2025-09 归档。

**Keep**（⭐⭐ 当前最佳开源告警管理）
- 协议：AGPL-3.0
- GitHub Stars：11,800+，last commit 活跃
- 特性：开源 AIOps 平台，告警聚合、路由、工作流，支持 Prometheus/Datadog/Sentry/Slack/Twilio
- 部署：Python + Next.js，Docker Compose

**Prometheus AlertManager + Google Calendar/本地日历 + Twilio 脚本**（⭐⭐ 手工方案）
- 特性：AlertManager 负责路由，日历管理值班表，脚本连接两者
- 成本：接近 $0，但需自行维护

**Squadcast**（⭐⭐ 商业但便宜）
- 定价：免费 5 人，Pro $9/人/月
- 特性：PagerDuty 的轻量替代品，有免费层

#### 建议
| 原版 | 最佳平替 |
|------|---------|
| Datadog | SigNoz（自托管）或 OpenObserve |
| PagerDuty | Keep（自托管）或 Squadcast（预算有限） |

---

### 3.7 CRM 与销售（HubSpot / Salesforce / Apollo / ZoomInfo）

#### 原版痛点
- HubSpot：$90/人/月（Professional）+ $1,500  onboarding 费
- Salesforce：$175/人/月（Enterprise）
- Apollo/ZoomInfo：B2B 数据服务，按查询量或席位收费极高

#### 开源自托管平替

**Twenty CRM**（⭐⭐⭐ 现代开源 CRM 首选）
- 协议：AGPL-3.0（核心）
- GitHub Stars：45,500+，2025-2026 增长最快的开源 CRM
- 特性：
  - 类 Salesforce 的数据模型（自定义对象、字段、关系）
  - GraphQL + REST API 双栈
  - Cloud 版原生 MCP 服务器（AI Agent 可直接查询 CRM 数据）
  - 现代化 UI（React/TypeScript），体验不像 2008 年的软件
- 自托管：Docker Compose，2C4G 起
- 成本：$0 许可，云主机 $20/月；Cloud $9/人/月
- 注意：营销自动化（邮件 campaign、滴灌序列）仍在开发中

**EspoCRM**（⭐⭐⭐ SMB 零代码首选）
- 协议：AGPL-3.0
- 特性：销售+营销+客服全模块、零代码配置、工作流自动化
- 部署：Docker 或 LAMP，技术门槛极低

**SuiteCRM**（⭐⭐⭐ 企业级深度）
- 协议：AGPL-3.0
- 特性：SugarCRM  fork，500 万+组织使用，报价、合同、项目管理全包
- 注意：PHP 老架构，UI 偏老旧，定制需技术投入

**Odoo CRM**（⭐⭐⭐ ERP+CRM 一体）
- 协议：LGPL-3.0（社区版）
- 特性：CRM 只是 Odoo 生态的一小部分，可扩展至进销存、财务、HR
- 注意：Odoo 社区版免费，但移动端和营销自动化需企业版（付费）

#### Apollo/ZoomInfo（B2B 数据）平替

> 开源社区**没有直接替代 Apollo/ZoomInfo 的免费方案**（商业数据本身就是资产）。但可用以下策略：

- **企查查 API / 天眼查 API**：国内企业工商数据，按量计费但国内访问快
- **LinkedIn Sales Navigator + 爬虫**（合规风险需注意）
- **自建爬虫 + 清洗管道**：用 Scrapy + Playwright 自建，配合大模型做 enrichment

#### 建议
| 原版 | 最佳平替 |
|------|---------|
| HubSpot | Twenty CRM 或 EspoCRM |
| Salesforce | SuiteCRM 或 Odoo CE |
| Apollo/ZoomInfo | 企查查 API + 自建爬虫（国内场景） |

---

### 3.8 数据平台与 BI（BigQuery / Snowflake / Databricks / Hex）

#### 原版痛点
- Snowflake/Databricks：企业级定价，按计算+存储计费，极易产生天价账单
- BigQuery：Google Cloud 生态，国内无法直接访问
- Hex：$36/人/月起，Notebook + BI 混合

#### 开源自托管平替

**ClickHouse**（⭐⭐⭐ 分析型数据库首选）
- 协议：Apache 2.0
- 特性：列式存储、向量化执行，单节点可处理 PB 级，Uber/Cloudflare 生产验证
- 部署：单二进制文件或集群
- 适用：替代 BigQuery/Snowflake 的核心分析引擎

**Apache Doris / StarRocks**（⭐⭐⭐ 国产/亚产实时数仓）
- 协议：Apache 2.0
- 特性：
  - 极速单表/多表 Join，亚秒级查询
  - 统一批流一体，同时服务 BI 报表和 Ad-hoc 查询
  - **中国团队主导开发**，社区文档中文友好
- 部署：Docker 或集群，兼容 MySQL 协议
- 适用：替代 Snowflake、ClickHouse 的国内部署场景

**Trino / Presto**（⭐⭐⭐ 联邦查询）
- 协议：Apache 2.0
- 特性：跨数据源（Hive/PostgreSQL/MySQL/S3）统一 SQL 查询
- 适用：替代 Databricks SQL / BigQuery 的联邦分析层

**Apache Superset**（⭐⭐⭐ BI 首选）
- 协议：Apache 2.0
- 特性：50+ 图表类型、SQL Lab、语义层、告警、多数据源
- 部署：Docker，支持几乎所有主流数据库
- 适用：替代 Hex / Looker / Tableau

**Metabase**（⭐⭐⭐ 非技术友好 BI）
- 协议：AGPL-3.0
- 特性：可视化查询构建器（无需 SQL）、仪表板订阅、嵌入分析
- 部署：Java jar 或 Docker，5 分钟启动
- 适用：替代 Hex / Google Data Studio

**JupyterHub**（⭐⭐⭐ 数据科学协作）
- 协议：BSD-3
- 特性：多用户 Jupyter Notebook 服务器，支持 Python/R/Julia
- 部署：Docker 或 Kubernetes
- 适用：替代 Hex 的 Notebook 协作场景

#### 建议
| 原版 | 最佳平替 |
|------|---------|
| Snowflake | Apache Doris 或 StarRocks |
| BigQuery | ClickHouse 或 Apache Doris |
| Databricks | Apache Spark（自托管）+ Trino |
| Hex | Metabase + JupyterHub 组合 |

---

### 3.9 营销与 SEO（Ahrefs / SimilarWeb / Klaviyo）

#### 原版痛点
- Ahrefs：$99/月起，SEO 工具定价天花板
- SimilarWeb：$199/月起，企业级数字情报
- Klaviyo：$20/月起 + 按联系人计费，邮件营销 SaaS

#### 开源/免费平替

**SerpBear**（⭐⭐ 自托管 SEO 排名追踪）
- 协议：MIT
- 特性：追踪关键词在 Google/Bing/Yandex 的排名、历史趋势、告警
- 部署：Node.js + SQLite，单容器
- 成本：$0 + 代理 IP 成本（如需大量查询）

**Google Search Console**（⭐⭐⭐ 免费官方）
- 特性：最权威的索引、排名、点击率数据，完全免费
- 限制：仅覆盖自己域名，无法查竞争对手

**Mautic**（⭐⭐⭐ 开源营销自动化）
- 协议：GPL-3.0
- 特性：邮件营销、滴灌序列、着陆页、A/B 测试、联系人评分、CRM 集成
- 部署：PHP + MySQL，Docker 可用
- 适用：替代 Klaviyo / HubSpot Marketing / Marketo

**Plausible Analytics**（⭐⭐⭐ 隐私友好网站分析）
- 协议：AGPL-3.0
- 特性：轻量（<1KB 追踪脚本）、欧盟托管、无 Cookie 横幅、实时数据
- 部署：Docker 或托管服务
- 适用：替代 Google Analytics / SimilarWeb（仅自己站点）

**Airbyte**（⭐⭐⭐ 开源数据集成）
- 协议：MIT / Elastic 2.0
- 特性：300+ 连接器，替代 Fivetran / Supermetrics 的数据管道层
- 部署：Docker 或 Kubernetes

#### 建议
| 原版 | 最佳平替 |
|------|---------|
| Ahrefs | SerpBear + Google Search Console 组合 |
| SimilarWeb | Plausible（自己站点）+ Semrush 免费层（竞品）|
| Klaviyo | Mautic（自托管） |
| Supermetrics | Airbyte + Metabase |

---

### 3.10 视频会议（Zoom）

#### 原版痛点
- Zoom：$14-20/人/月，mcp-us.zoom.us 需翻墙

#### 开源自托管平替

**Jitsi Meet**（⭐⭐⭐ 首选）
- 协议：Apache 2.0
- 特性：WebRTC 视频、屏幕共享、端到端加密（2 人时）、房间密码、录制
- 部署：官方 Docker 镜像，2C4G 可支持 20-30 人会议
- 成本：$0 许可，服务器 $10-20/月
- 公共实例：meet.jit.si（免费，但数据走 8x8 服务器）

**BigBlueButton**（⭐⭐⭐ 教育场景）
- 协议：LGPL-3.0
- 特性：白板、分组讨论室、投票、实时笔记、LTI 集成
- 部署：Ubuntu 专用，面向在线教学优化

**LiveKit**（⭐⭐ 开发者首选）
- 协议：Apache 2.0
- 特性：现代 WebRTC SFU，SDK 丰富（React/Flutter/React Native/Unity）
- 部署：Go 单二进制，或云托管

#### 国内免费商用平替
- **腾讯会议**：国内普及率最高，免费版 100 人/60 分钟
- **飞书会议**：与飞书生态集成，字幕翻译强
- **钉钉会议**：与审批/日程深度联动

---

### 3.11 文件存储、法务与电子签名（Box / DocuSign / Egnyte）

#### 原版痛点
- Box：$5-15/人/月，mcp.box.com 需翻墙
- DocuSign：$10-40/人/月，按信封数限制
- Egnyte：企业级文件同步，定价高

#### 开源自托管平替

**Nextcloud**（⭐⭐⭐ 全能网盘）
- 协议：AGPL-3.0
- 特性：文件同步/分享、协作办公（OnlyOffice/Collabora 集成）、日历、邮件、通讯录、Talk（视频会议）
- 部署：Docker 或 Snap，官方文档详尽
- 生态：App Store 有数百插件，包括电子签名（LibreSign）、项目管理（Deck）、笔记（Notes）
- 适用：一台 Nextcloud 可替换 Box + Google Calendar + Gmail + Zoom（Talk）

**Seafile**（⭐⭐⭐ 极致同步性能）
- 协议：AGPL-3.0
- 特性：类 Git 的块级文件同步，10万+ 文件秒同步
- 部署：Docker 或二进制

**DocuSeal**（⭐⭐⭐ 电子签名首选）
- 协议：AGPL-3.0
- GitHub Stars：16,000+
- 特性：PDF/DOCX/XLSX 表单创建、多签署方、自定义字段、API & Webhook、审计日志、白标
- 部署：Docker，支持 SQLite/PostgreSQL/MySQL
- 成本：$0 许可，服务器 $9/月；Cloud 免费层 10 封/月
- 合规：HIPAA、GDPR、SOC 2

**Documenso**（⭐⭐⭐ 现代美观）
- 协议：AGPL-3.0
- 特性：Next.js + TypeScript 现代栈、团队管理、SSO、审计追踪
- 部署：需 PostgreSQL

**LibreSign**（⭐⭐ Nextcloud 插件）
- 协议：AGPL-3.0
- 特性：集成在 Nextcloud 内的电子签名，无需额外部署
- 注意：需 Ghostscript 生成可见签名，移动端体验一般

#### 建议
| 原版 | 最佳平替 |
|------|---------|
| Box / Egnyte | Nextcloud（可扩展为全套办公套件） |
| DocuSign | DocuSeal 或 Documenso |

---

### 3.12 办公套件（Google Workspace / MS365）

#### 原版痛点
- Google Calendar / Gmail：`.mcp.json` 中 URL 为空（需自行配置），国内访问困难
- MS365：microsoft365.mcp.claude.com 是 Anthropic 代理，稳定性未知

#### 开源自托管平替

**Nextcloud + OnlyOffice / Collabora Online**（⭐⭐⭐ 全套替代）
- OnlyOffice：AGPL-3.0，Microsoft Office 的高兼容替代品
- Collabora Online：MPL-2.0，LibreOffice 的在线版
- 组合效果：文档/表格/演示的实时协作编辑，与 MS Office 格式 99% 兼容
- 部署：Nextcloud Docker + OnlyOffice Document Server Docker

**SOGo**（⭐⭐ 日历+邮件）
- 协议：GPL-2.0
- 特性：Webmail、日历、通讯录，Exchange ActiveSync 兼容
- 部署：Docker 或包管理器

**Radicale**（⭐⭐ 轻量日历/通讯录服务器）
- 协议：GPL-3.0
- 特性：CalDAV/CardDAV，极简，单 Python 进程

#### 国内免费商用平替
- **钉钉**：邮件（钉邮）、日历、文档、会议全套
- **飞书**：邮箱、日历、文档、会议、OKR 全套
- **WPS+**：金山办公的企业版，国内兼容 Office 最佳

---

### 3.13 生物研究工具

#### 原版盘点

| 原版 | 本质 | 是否免费 |
|------|------|---------|
| PubMed | NCBI 文献数据库 | ✅ 本身就是免费的！ |
| bioRxiv | 预印本平台 | ✅ 本身就是免费的！ |
| ChEMBL | 化学分子数据库 | ✅ 本身就是免费的！ |
| Clinical Trials | 临床试验注册库 | ✅ 本身就是免费的！ |
| Open Targets | 药物靶点平台 | ✅ 本身就是免费的！ |
| Synapse | Sage Bionetworks 数据平台 | ✅ 基础功能免费 |
| Consensus | AI 文献摘要 | ⚠️ 有免费层 |
| BioRender | 科学插图绘制 | ❌ 收费（$35/人/月） |
| Benchling | 实验记录+序列分析 | ❌ 企业收费（.mcp.json URL 为空） |
| Owkin | 医学 AI 平台 | ❌ 企业收费 |
| Wiley | 学术出版商 | ❌ 收费 |

#### 平替策略

**好消息**：生物研究插件中的大部分数据源（PubMed、bioRxiv、ChEMBL、ClinicalTrials.gov、Open Targets）**本身就是免费的公共学术资源**。它们的问题不是收费，而是：
1. 访问速度在中国大陆较慢（PubMed 偶有镜像）
2. 没有 MCP 封装，需自行构建 API 层

**BioRender 平替**（⭐ 较弱）
- **Bioicons**：免费开源的科学图标库（CC 协议）
- **Inkscape** + 科学插件：矢量绘图软件，配合科学符号插件手动绘制
- **Biorender 的免费教育版**：部分高校可申请
- 坦诚讲：**没有完美平替**，BioRender 的核心价值是模板和素材库

**Benchling 平替**（⭐⭐）
- **Open Vector Editor**：开源质粒/序列编辑器
- **SnapGene Viewer**：免费查看器（非开源）
- **GenBank + 自建 LIMS**：用 PostgreSQL + 简单前端自建实验记录系统

**Python 依赖平替**（`bio-research/skills/instrument-data-to-allotrope/requirements.txt`）
- `allotropy`、`pandas`、`openpyxl`、`pdfplumber` 均为 PyPI 开源包
- 国内可用 **清华大学 TUNA、阿里云 PyPI 镜像**加速安装

---

### 3.14 PDF 处理

#### 原版
- `pdf-viewer` 插件使用 `npx -y @modelcontextprotocol/server-pdf`

#### 平替
- **该包本身就是开源的**！属于 ModelContextProtocol 官方 servers 仓库：
  - GitHub：`github.com/modelcontextprotocol/servers`
  - 协议：MIT
- 此外，PDF 处理的开源工具极多：
  - **pdfplumber**（Python）：PDF 文本/表格提取
  - **PyMuPDF (fitz)**：极速 PDF 渲染与操作
  - **pdf.js**（Mozilla）：浏览器内 PDF 渲染

---

## 4. 中国大陆可用性专项

### 4.1 "国内直连"判定标准

| 等级 | 定义 |
|------|------|
| ✅ 无需代理 | 在中国大陆宽带/4G 环境下可直接访问 |
| ⚠️ 有条件可用 | 需自托管在国内服务器，或偶有波动 |
| ❌ 需要代理 | 域名/IP 被阻断或访问极慢 |

### 4.2 原版 SaaS 国内可用性

| 服务 | 国内可用性 | 说明 |
|------|-----------|------|
| GitHub | ⚠️ | 基础访问可用，但 Copilot API 不稳定 |
| Notion | ❌ | 间歇性阻断 |
| Slack | ❌ | 完全阻断 |
| Linear | ❌ | 完全阻断 |
| Figma | ⚠️ | 访问慢，偶有阻断 |
| Datadog | ❌ | 完全阻断 |
| Zoom | ⚠️ | Zoom 国际版国内访问差，Zoom 中国版已转由 WebEx 替代 |
| Google 全家桶 | ❌ | 完全阻断 |
| PubMed | ⚠️ | 基础可用，速度较慢 |

### 4.3 国内部署策略

**最佳实践：国内服务器自托管**

1. 购买国内云服务器（阿里云/腾讯云/华为云，2C4G 约 ￥100-200/月）
2. 备案域名（如需 80/443 端口）
3. 部署 Docker 版开源工具
4. 通过 Nginx/Traefik 反代 + HTTPS
5. Claude Code 通过国内可访问的域名连接 MCP 服务器

**镜像加速**
- Docker Hub：使用阿里云/腾讯云镜像加速器
- PyPI：清华 TUNA、阿里云 PyPI 镜像
- npm：淘宝 npm 镜像、腾讯云 npm 镜像

---

## 5. 自托管成本估算

> 以支撑一个 **20 人团队**的日常使用为基准，全部使用开源方案自托管。

| 服务类别 | 开源方案 | 推荐配置 | 月成本（国内云） |
|---------|---------|---------|----------------|
| 项目管理 | Plane | 2C4G | ￥100-150 |
| 文档/Wiki | Docmost / AFFiNE | 2C4G | ￥100-150 |
| 代码托管 | Gitea | 1C2G | ￥50-80 |
| 设计 | Penpot | 2C4G | ￥100-150 |
| 监控/APM | SigNoz | 4C8G | ￥200-300 |
| CRM | Twenty CRM | 2C4G | ￥100-150 |
| BI | Metabase + ClickHouse | 2C4G | ￥100-150 |
| 网盘/办公套件 | Nextcloud + OnlyOffice | 2C4G + 100G 存储 | ￥150-200 |
| 视频会议 | Jitsi Meet | 4C8G | ￥200-300 |
| 电子签名 | DocuSeal | 1C2G | ￥50-80 |
| **总计** | | | **￥1,150-1,910/月** |

### 对比原版 SaaS 成本

| 方案 | 20 人月成本估算 |
|------|----------------|
| 全自托管开源 | ￥1,150-1,910 |
| 全商业 SaaS（Slack+Linear+Notion+Figma+Datadog+HubSpot+Zoom）| ￥15,000-30,000+ |
| **节省** | **~90-95%** |

### 隐性成本提醒

- **维护人力**：自托管需要 0.1-0.3 FTE 的运维投入
- **数据备份**：需自行配置自动化备份（Restic/BorgBackup 到对象存储）
- **安全更新**：需及时跟进 CVE 补丁
- **网络备案**：国内服务器 80/443 端口需 ICP 备案

---

## 6. 推荐组合方案

### 方案 A：极致开源自托管（数据主权优先）

| 功能 | 工具 | 部署方式 |
|------|------|---------|
| IM | Mattermost | Docker |
| 项目管理 | Plane | Docker |
| 知识库 | Docmost | Docker |
| 设计 | Penpot | Docker |
| 代码托管 | Gitea | Docker |
| 监控 | SigNoz + Uptime Kuma | Docker |
| CRM | Twenty CRM | Docker |
| BI | Metabase + ClickHouse | Docker |
| 网盘/办公 | Nextcloud + OnlyOffice | Docker |
| 会议 | Jitsi Meet | Docker |
| 签名 | DocuSeal | Docker |

- **适合**：技术能力强、数据主权要求高的团队（金融、医疗、政府、跨境合规）
- **优点**：零订阅费、完全可控、无 vendor lock-in
- **缺点**：需要专职运维或技术负责人

### 方案 B：国内 SaaS 组合（体验优先）

| 功能 | 工具 | 费用 |
|------|------|------|
| IM/文档/会议 | 飞书 | 免费版够用 |
| 项目管理 | 飞书项目 / 阿里云效 | 免费版 |
| 设计 | 即时设计 | 免费版 |
| 代码托管 | Gitee / GitLab 国内版 | 免费私有仓 |
| 监控 | 阿里云 ARMS（按量）| ￥0-500/月 |
| CRM | 纷享销客 / 销售易 | 按席位 |
| BI | 阿里云 Quick BI / Metabase 自托管 | 混合 |
| 网盘 | 阿里云盘企业版 / 坚果云 | 按容量 |
| 签名 | 法大大 / e签宝 | 按份数 |

- **适合**：国内中小企业、追求开箱即用、团队技术能力有限
- **优点**：无需运维、国内客服、合规省心
- **缺点**：长期订阅成本、数据在第三方、功能受限

### 方案 C：混合架构（推荐）

| 功能 | 工具 | 策略 |
|------|------|------|
| 核心知识产权（设计/文档/代码）| Penpot + AFFiNE + Gitea | **自托管** |
| 高频协作（IM/会议）| 飞书 / 钉钉 | **国内 SaaS** |
| 基础设施（监控/BI）| SigNoz + Metabase | **自托管** |
| 商务工具（CRM/签名）| Twenty CRM + DocuSeal | **自托管** |
| 外部数据（B2B/工商）| 企查查 API | **国内 SaaS** |

- **适合**：大多数成长型团队
- **核心原则**：**核心资产自托管，通用协作用 SaaS**

---

## 7. 结论与行动建议

### 核心结论

1. **knowledge-work-plugins 仓库本身完全自由**：零依赖、Apache 2.0、纯 Markdown/JSON，可在中国大陆任意使用、修改、分发。

2. **真正的问题在外部 MCP 连接器**：40+ 外部服务中，90% 是收费闭源 SaaS，70% 在中国大陆访问困难。

3. **每个服务都有出路**：本报告为几乎所有服务提供了至少一条免费/开源/自托管替代路径。不存在"无路可走"的插件。

4. **最佳策略是混合架构**：核心知识资产（文档、设计、代码）自托管；通用协作（IM、会议）用国内 SaaS；数据基础设施（监控、BI、数仓）视团队能力选择自托管或国内云。

### 立即行动清单

| 优先级 | 行动 | 预计时间 |
|--------|------|---------|
| P0 | 在 `.mcp.json` 中为各插件添加**国内可访问的替代服务端点**（如自建 Plane API、Gitea API、Docmost API） | 1-2 周 |
| P0 | 为 `bio-research` 的 `requirements.txt` 添加**国内 PyPI 镜像安装说明** | 10 分钟 |
| P1 | 编写 **"中国区部署指南"** 文档，汇总各插件的国内替代方案与配置方法 | 1 周 |
| P1 | 调研并适配 **钉钉/飞书 Bot API** 作为 Slack MCP 的国内替代连接器 | 1-2 周 |
| P2 | 为热门插件（engineering、product-management、design）创建**自托管友好版**的 `.mcp.json` 配置示例 | 3-5 天 |
| P2 | 评估将部分第三方 Marketplace 插件（如 Apollo、ZoomInfo）替换为**国内数据源**（企查查、天眼查）的可行性 | 1 周 |

### 最后的坦诚

> 开源工具不是银弹。自托管意味着你成了自己的运维团队。但对于"中国大陆可用+不花钱+数据自主"这三重目标，开源自托管是**唯一同时满足三条的路径**。如果你只能满足两条，那么：
> - 不花钱 + 国内可用 → 用飞书/钉钉/语雀等国内免费 SaaS
> - 国内可用 + 数据自主 → 自建 + 国内服务器
> - 不花钱 + 数据自主 → 自建 + 海外服务器（需代理访问）

本报告尽可能保持客观，标明了每个平替的优缺点和适用边界。希望它能帮助你在 knowledge-work-plugins 的基础上，构建一套真正可用、可控、可持续的知识工作基础设施。

---

*报告完成。如需针对某一具体插件展开更详细的 MCP 连接器适配方案，可继续深入。*
