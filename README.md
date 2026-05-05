# palm-mcp-server

[![CI](https://github.com/fugui-legion/palm-mcp-server/actions/workflows/ci.yml/badge.svg)](https://github.com/fugui-legion/palm-mcp-server/actions/workflows/ci.yml)
[![npm version](https://img.shields.io/npm/v/palm-mcp-server)](https://www.npmjs.com/package/palm-mcp-server)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![MCP](https://img.shields.io/badge/MCP-1.29+-blue)](https://modelcontextprotocol.io)

掌心人格局 MCP Server — 基于 Model Context Protocol 的合规检查、报告查询和文案生成服务。3 Tools + 3 Resources + 3 Prompts，STDIO/Streamable HTTP 双传输。

## 快速开始

```bash
pnpm install
pnpm dev        # 开发运行 (tsx watch)
pnpm build      # 生产构建
pnpm typecheck  # 类型检查
pnpm test       # 运行测试
```

## MCP 能力

### 3 Tools

| 工具 | 描述 | 必填参数 |
|------|------|----------|
| `check_compliance` | 27项禁用词检查，返回违规词和替换建议 | `text: string` |
| `query_reports` | 查询人格报告(ID/类型筛选) | 可选: `reportId`, `personaType`, `limit` |
| `analyze_persona` | 五维人格分析+社交文案生成 | `openness...neuroticism`: 0-100 ×5 |

### 3 Resources

| URI | 内容 |
|-----|------|
| `palm://compliance/terms` | 27项禁用词(5类分组: 命理/命运/关系/绝对化/灾祸) |
| `palm://compliance/replacements` | 禁用词→替换建议映射表 |
| `palm://persona/dimensions` | 五维人格维度(大五模型)说明 |

### 3 Prompts

| Prompt | 用途 | 参数 |
|--------|------|------|
| `analyze_report` | 全面分析人格报告 | `report_id` (必填) |
| `draft_caption` | 生成社交文案(weapp/tt) | `persona_type` (必填), `platform` |
| `compliance_check` | 三级合规审查 | `report_id` (必填) |

## 配置 Claude Code

```json
{
  "mcpServers": {
    "palm": {
      "command": "npx",
      "args": ["palm-mcp-server"]
    }
  }
}
```

## API 示例

```typescript
// check_compliance 调用
const result = await mcp.callTool('check_compliance', {
  text: '手相算命看正缘'
});
// → { safe: false, violations: [{word:'手相', replacement:'手掌特征'}, ...] }

// analyze_persona 调用
const report = await mcp.callTool('analyze_persona', {
  openness: 78, conscientiousness: 65,
  extraversion: 42, agreeableness: 88, neuroticism: 35
});
// → { dominant: {dimension:'agreeableness', score:88, label:'宜人性'}, ... }
```

## 合规说明

满足微信小程序审核、中国AI内容监管要求：
- **27项禁用词**自动检测+替换建议
- **5类分组**: 命理(5) · 命运(6) · 关系(7) · 绝对化(5) · 灾祸(4)
- **三级审查**: 阻断(严重违规) · 警告(擦边) · 建议(优化)
- **免责声明**: 所有分析输出标记"仅供娱乐，不构成专业建议"

## 项目结构

```
palm-mcp-server/
├── src/index.ts          # MCP Server 主入口 (222行)
├── package.json
├── tsconfig.json
├── LICENSE               # MIT
├── README.md
└── .github/workflows/
    └── ci.yml            # Typecheck + Build + Test
```

## 技术栈

| 层面 | 选型 |
|------|------|
| 语言 | TypeScript (strict mode) |
| 运行时 | Node.js ≥20 |
| MCP SDK | @modelcontextprotocol/sdk ^1.29 |
| 传输层 | STDIO + Streamable HTTP |
| 包管理 | pnpm |
| 测试 | Vitest |
| 许可 | MIT |

## 作者

**富贵军团** (fugui-legion) — [AI师生研究院 V-6.1](https://github.com/fugui-legion)

> "不教人编程，教AI如何教AI。这是递归的，也是唯一的。"
