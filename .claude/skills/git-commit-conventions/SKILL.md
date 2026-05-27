---
name: git-commit-conventions
description: Use when committing code to the Grad-School-Agent repository — defines the project-specific Conventional Commits format (8 allowed types) with Chinese descriptions plus English technical terms, and records GitHub remote info.
metadata:
  type: project-skill
  scope: Grad-School-Agent
---

# Grad-School-Agent — Git 提交规范

本 skill 定义了 **Grad-School-Agent** 项目专属的 Git 提交规范、分支策略以及 GitHub 远程仓库信息。每次对本仓库执行 `git commit` 之前必须遵循该规范。

---

## 1. 仓库基本信息

| 项目项 | 值 |
| :--- | :--- |
| 仓库名称 | `Grad-School-Agent` |
| 远程仓库 (origin) | <https://github.com/xiaowenhao404/Grad-School-Agent> |
| 默认分支 | `main` |
| 协议 | HTTPS |
| 默认 GitHub 用户 | `xiaowenhao404` |

```bash
# 查看远程仓库
git remote -v

# 若尚未配置，执行
git remote add origin https://github.com/xiaowenhao404/Grad-School-Agent.git
```

---

## 2. 提交消息格式

```text
<type>: <中文说明（专业名词保留英文）>
```

- **type**：必须为下表中 8 个允许类型之一，**全小写英文**。
- **说明文字**：使用中文撰写，专业名词（如 `Transformer`、`Hydra`、`PyTorch`、`RLHF`、`Conventional Commits` 等）保留英文原样。
- **冒号后须有一个空格**。
- **首字母不大写、句末不加句号**。
- subject 长度建议不超过 **50 个字符**（中文按 2 个字符计算亦不超过约 25 个汉字）。

### 2.1 允许的 type 列表（仅以下 8 种）

| Type | 含义 | 中文示例 |
| :--- | :--- | :--- |
| `feat` | 新增功能（Feature） | `feat: 新增基于 Transformer 的意图识别模块` |
| `fix` | 修复 Bug | `fix: 修复 tokenizer 中文标点切分错误` |
| `docs` | 仅文档的修改（Documentation） | `docs: 补充 README 安装说明` |
| `style` | 不影响代码含义的格式调整（空格、缩进、缺少分号等） | `style: 格式化 data_loader.py 缩进` |
| `refactor` | 代码重构（既非新增功能也非修 Bug） | `refactor: 抽取通用的 logger 工具函数` |
| `test` | 添加缺失测试或修正现有测试 | `test: 补充 data 模块的单元测试` |
| `chore` | 构建过程或辅助工具与库的更改（如依赖升级、文档生成工具） | `chore: 升级 transformers 至 4.45` |
| `perf` | 提高性能的代码更改（Performance） | `perf: 优化 batch 数据加载吞吐` |

> ⚠️ **本项目不使用** `build`、`ci`、`revert` 等其他常见 type — 请严格限定在以上 8 种之内。

### 2.2 提交消息示例

✅ **合规示例**

```text
feat: 新增基于 Hydra 的配置加载流程
fix: 修复 PyTorch DataLoader 多进程死锁
docs: 更新 README 中的 Conventional Commits 示例
style: 移除多余空行并格式化 imports
refactor: 拆分 trainer 模块为 base 与 callbacks
test: 为 prompt builder 添加边界用例
chore: 添加 .gitignore 并配置远程仓库
perf: 用向量化替换循环计算 attention mask
```

❌ **不合规示例**

```text
Add new feature           # 缺少 type 前缀
feat:新增功能。            # 缺少空格，且句末多加句号
build: 升级 webpack       # 本项目未启用 build 类型
update files              # 信息模糊、缺少 type
FEAT: 大写的 type          # type 必须小写
```

### 2.3 可选 body 与 footer

对于较复杂改动可附 body 说明动机与影响（与 subject 间空一行）：

```text
refactor: 重构 trainer 模块为可插拔结构

将原本耦合在 Trainer 类中的 evaluation / checkpoint 逻辑
抽取为独立的 Callback 子类，便于后续按实验自由组合。

BREAKING CHANGE: Trainer.run() 的回调注册方式变更
```

---

## 3. 分支策略（简化版）

针对课程项目，默认采用轻量分支模型：

| 分支 | 用途 | 生命周期 |
| :--- | :--- | :--- |
| `main` | 可发布的主分支 | 永久 |
| `feature/<功能名>` | 新功能开发 | 完成后合并并删除 |
| `bugfix/<问题描述>` | Bug 修复 | 完成后合并并删除 |

分支命名使用 **kebab-case**，必要时可前缀 issue 编号：

```text
feature/intent-classifier
feature/12-add-data-augmentation
bugfix/tokenizer-cjk
```

合并策略：

- feature 同步 main 使用 `git rebase main`
- feature 合并回 main 使用 `git merge --no-ff`，保留分支历史

---

## 4. 日常工作流

```bash
# 1. 同步主分支
git checkout main
git pull origin main

# 2. 创建功能分支
git checkout -b feature/intent-classifier

# 3. 开发、提交（遵循上文规范）
git add <file>
git commit -m "feat: 新增意图分类基线模型"

# 4. 推送到远程
git push -u origin feature/intent-classifier

# 5. 在 GitHub 发起 Pull Request 并合并

# 6. 清理分支
git branch -d feature/intent-classifier
git push origin -d feature/intent-classifier
```

---

## 5. 提交前检查清单

提交前请逐项确认：

- [ ] type 在 8 种允许类型范围内
- [ ] type 与冒号之间无空格，冒号后有 1 个空格
- [ ] 中文说明清晰具体，无「update」「修改一下」等模糊措辞
- [ ] 单次提交聚焦一个逻辑变更，避免混合无关修改
- [ ] 未提交 secrets / API key / `.env` 等敏感信息
- [ ] 已通过本地基础验证（脚本能运行 / 测试通过）

---

## 6. 应用规则

调用本 skill 的触发场景：

- 当用户在 Grad-School-Agent 仓库内提到「commit」「提交」「git push」「写一条 commit message」「rebase」「合并分支」等关键词。
- 当 Claude 在该仓库执行 `git commit -m ...` 之前。
- 当用户询问本项目的 Git 规范或远程仓库地址。

如有变更（例如新增 type 或调整分支策略），同时更新本文件与项目 `README.md` 中的对应章节。
