# Repository Guidelines

## Product and Authority

REA is a local-only reverse-engineering tool with shared CLI/MCP workflows. Distinguish observations, derivations, inferences, and unknowns; preserve Evidence provenance and session semantics through both adapters. Provider support and setup live in [README.md](README.md#choosing-a-deep-analysis-provider) and its linked guides.

Ghidra uses ephemeral databases without modifying executable bytes or controlling a GUI; Windows Ghidra P0 has no mutation authority. IDA analysis is read-only; never save or close an attached GUI database.

Configuration changes must be additive and idempotent, with backups. Installers must not install or upgrade unrelated software, including Homebrew, Node.js, npm, Java, or Ghidra. Ghidra and IDA are bring-your-own. `rea setup` must show its planned changes and require approval before writing files or installing Hopper.

Preserve caller-selected inputs and local evidence, including output, URLs, paths, digests, and metadata. Redact transport authentication credentials and explicitly marked sensitive values; do not infer secrecy from names or text patterns. Do not persist the ambient environment merely because a child inherits it.

## Architecture and Cleanup

Dependencies flow inward: domain semantics and contracts, provider adapters, shared application workflows, then CLI/MCP adapters. Keep engine protocols and provider-specific code out of domain/application layers. See [docs/architecture.mermaid](docs/architecture.mermaid) when changing composition.

Give each interpretation and resource a clear owner. Preserve identity, omission, uncertainty, and source evidence across boundaries. Retain ownership of resources whose cleanup failed. Reuse `src/process/` supervision and identity primitives; a PID and executable path alone do not establish ownership after exit or reuse.

Assess brittleness in touched code during implementation, debugging, review, and maintenance. Fix the producing representation or ownership rule before adding downstream repairs. Normalize once at the owning boundary using format-aware parsers. Remove superseded representations, aliases, wrappers, and redundant tests after verifying affected consumers. Introduce abstractions only when they remove observed complexity.

Parse unknown boundary values and model expected failures with the tagged error algebra and `Result`. Preserve partial facts and meaningful failure reasons. Keep identity and lookup values separate from display formatting; preserve source values and explicit unknowns when normalization loses information. Absolute paths, file URLs, and HTTP paths have different semantics.

Validate advertised JSON Schemas after SDK conversion against their declared dialect and actual clients. Bind handlers to named contracts with exact types; catalog ordering must never select an operation or schema.

## Development

Use [.nvmrc](.nvmrc) and `package.json#packageManager` for the pinned toolchain. Cursor Cloud may place older Node.js first on `PATH`; environment setup installs the pinned toolchain under `/usr/local/bin`.

- `npm ci`: install locked dependencies.
- `npm run build:cached`: build the runtime and packaged skill.
- `npm run test:local -- PATH...`: source tests without building.
- `npm run test:focused -- PATH...`: exact tests with their required artifacts.
- `npm run check:fast`: cached typecheck/lint and the pre-push check.
- `npm run docs:check`: validate generated documentation.

Use relevant checks for the change. The complete local gate, `npm run check:pr`, is optional for broad changes or CI diagnosis. See [CONTRIBUTING.md](CONTRIBUTING.md) for development, generated-file ownership, and release conventions.

`docs/public/product-catalog.json`, `docs/verification/managed-conformance-*.json`, and `skills/` are ignored outputs. Edit source contracts and `.agents/skills/reverse-engineer-anything/`, then regenerate as needed. `.cache/mcp-tool-catalog.json` is test metadata; runtime builds and source checking do not consume it. Never commit binaries, provider project documents, credentials, `dist/`, `node_modules/`, or local planning artifacts.

## Tool and Test Changes

For tool design, follow [docs/tool-design.md](docs/tool-design.md): start from the analyst question, reuse existing contracts, preserve caller choices, and return useful evidence inline. Limits must follow real format, protocol, authority, or resource constraints. Account for representation expansion before allocation; derive completeness from examined coverage.

For test changes or provider claims, use [docs/testing.md](docs/testing.md). Prefer real public CLI/MCP workflows, then production-boundary integration, then producer goldens. Keep module tests for distinct semantics stronger workflows cannot reliably reproduce. Preserve malformed-input, cancellation, cleanup, permission, capacity, and target-identity coverage when pruning. Report unverified coverage explicitly; simulated providers do not establish real-provider behavior.

Use Conventional Commit subjects and PR titles. Mark breaking changes with `!` or a `BREAKING CHANGE:` footer. Describe behavior changes, relevant validation, and real-provider verification scope.

<!-- BEGIN:turborepo-agent-rules -->

# This is NOT the Turborepo you know

Turborepo configuration, task behavior, and CLI commands can vary between installed versions and may differ from your training data. Resolve the `turbo` package from this file's directory or relevant workspace; in monorepos, it may not be visible from the repository root. For example, run `node -p "require.resolve('turbo/package.json')"` from a workspace that depends on `turbo`.

Read `docs/README.md` inside that installed package first, then read the relevant pages from its `docs/` directory before changing Turborepo configuration or commands. Heed deprecation notices. These bundled docs match the installed package version and are available without network access.

This block is written and re-added by `turbo` before repository-scoped commands when an AI agent is detected. In the Turborepo source repository, its template is defined in `crates/turborepo-cli/src/cli/agent_guidance.rs`. Removing the managed block while updates are enabled means a later qualifying invocation will add it again. Set `"agentGuidance": false` in the root `turbo.json` or `turbo.jsonc` to opt out; this does not remove an existing block. Keep the block committed with your work to avoid an uncommitted change on the next agent invocation.
<!-- END:turborepo-agent-rules -->

## GitHub PR 交付规范（供 Notion 同步，2026-10-13）

- **适用范围**：未来需要交付到 GitHub 的真实代码或文档任务。一项独立交付对应一个可审查 PR；禁止为凑同步记录制造空 PR、虚假改动或无差异的历史补单。
- **开始前**：检查 `git status`、远端和实际默认分支，不假定都是 `main`。保留其他 Agent 的未提交改动和活动分支；采用独立任务分支或 worktree，不代提交、清理或切换他人的工作区。仅通过 GitHub API 执行时，核对仓库、默认分支、活动 PR、分支 HEAD 和目标文件版本，保留本地工作区。
- **交付路径与授权**：沿用本仓现有任务分支命名，完成必要验证后按实际范围组织 commit。对于既定范围内的真实任务，wind 已长期授权推送任务分支，并创建或更新指向已核实实际默认分支的 Draft 或正式 PR，无需逐次确认 Push/PR；后续明确限制、只读范围、运行时权限和仓库保护仍优先。commit 仍遵守本仓现行规则。不得直接推送默认分支；合并、发布、部署、force push、历史改写及无关远程动作仍须 wind 明确批准。本条取代旧的通用逐次 Push/PR 确认要求，不扩大生产、数据、密钥或系统权限。
- **PR 质量**：标题写明任务和结果；描述包含变更范围、实际验证及结果、未验证项、风险、回滚方式和关联任务。回执附 PR 链接、目标分支和待审状态；PR 已创建、CI 通过不等于验收、合并或部署。私有信息、密钥、客户资料不进入 PR 标题、描述或 diff。
- **Notion 同步证据**：Notion 的 GitHub PR 同步数据库记录的是 PR；普通 commit、PR 标题或手工补录不证明自动同步。只有核实数据库中的实际 GitHub URL、仓库和 PR 状态后才报告已同步；否则报告“同步未核实”。
- **Notion 任务看板（协议 v3，仅关联任务适用）**：只有任务明确关联 Notion「Notion AI 的待办」、实际任务编号或用户指定该工作流时，才读取对应卡片及协议。普通本地任务不强制建卡或查信。共享看板：`https://app.notion.com/p/fdd495ec62664d1dbfe63a3f6c3a7351`；协议：`https://app.notion.com/p/9dda63541ab94c069dd02d0fee6efc4b`。按卡片目标、验收、项目和“需评审”执行；开工填入自己的“执行方”、保留已有执行方，并改为“进行中”。
- **实际编号与回填**：从对应任务卡核实完整编号；PR 标题以 `[实际编号]` 开头，描述写 `Refs 实际编号`，不得猜测或硬编码编号前缀，无法核实时如实说明。PR 链接填入卡片“证据”字段，保留既有有效证据；补充背景和原始 commit SHA、CI、文件或日志链接写入卡片或 PR。标题本身不证明任务自动关联。
- **评审与完成边界**：勾选“需评审”的任务送审时改为“待评审”；评审通过前不得合并，通过后仍须 wind 批准具体合并动作。执行方不得将需评审卡片标为“完成”，由 Notion AI / wind 在通过评审且 PR 已合并后确认。无需评审的代码任务，PR 未合并时保持“进行中”，核实合并并保留证据后才可标为“完成”。架构、花钱、不可逆操作、正式交付或跨项目影响按协议评审门槛处理。
- **修改回复与更新频率**：收到“需修改”后改为“进行中”，逐条回应 P0/P1，已改附原始证据，不改写理由；回应写在卡片评审结论下，准备复审后改为“待评审”。Notion 只在状态切换、送审或修改回复时更新，不写持续进度报告，不自行发信、触发 Notion AI 评审或假定评审通过。访问或回填失败时如实报告缺口，继续独立授权工作。
- **既有边界**：上述交付规范只统一分支、PR 和任务证据流程；项目原有产品、数据真实性、测试、隐私和安全限制继续有效。已在运行的 Agent 是否重新加载本文件须另行验证。
