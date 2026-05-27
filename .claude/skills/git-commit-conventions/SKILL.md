---
name: git-commit-conventions
description: Use when committing code to the Grad-School-Agent repository — defines the project-specific Conventional Commits format (8 allowed types) with Chinese descriptions plus English technical terms, and records GitHub remote info.
---

# Grad-School-Agent — Git 提交规范

本 skill 定义了 **Grad-School-Agent** 项目的 Git 提交规范、分支策略与远程仓库信息。每次在本仓库执行 `git commit` 之前必须遵循该规范。

---

## 1. 提交消息格式

```text
<type>: <中文说明（专业名词保留英文）>
```

- **type**：必须为下表中 8 个允许类型之一，**全小写英文**。
- **说明文字**：使用中文撰写，专业名词（如 `Transformer`、`Hydra`、`PyTorch`、`RLHF`、`Conventional Commits` 等）保留英文原样。
- **冒号后须有一个空格**。
- **首字母不大写、句末不加句号**。
- subject 长度建议不超过 **50 个字符**（中文按 2 字符计，不超过约 25 汉字）。

### 允许的 type（仅以下 8 种）

| Type | 含义 | 示例 |
| :--- | :--- | :--- |
| `feat` | 新增功能（Feature） | `feat: 新增基于 Transformer 的意图识别模块` |
| `fix` | 修复 Bug | `fix: 修复 tokenizer 中文标点切分错误` |
| `docs` | 仅文档的修改（Documentation） | `docs: 补充 README 安装说明` |
| `style` | 不影响代码含义的格式调整 | `style: 格式化 data_loader.py 缩进` |
| `refactor` | 代码重构（既非新增功能也非修 Bug） | `refactor: 抽取通用的 logger 工具函数` |
| `test` | 添加缺失测试或修正现有测试 | `test: 补充 data 模块的单元测试` |
| `chore` | 构建过程或辅助工具与库的更改 | `chore: 升级 transformers 至 4.45` |
| `perf` | 提高性能的代码更改（Performance） | `perf: 优化 batch 数据加载吞吐` |

> ⚠️ 本项目不使用 `build`、`ci`、`revert` 等其他 type，请严格限定在以上 8 种之内。

### 合规示例

```
feat: 新增基于 Hydra 的配置加载流程
fix: 修复 PyTorch DataLoader 多进程死锁
docs: 更新 README 中的 Conventional Commits 示例
style: 移除多余空行并格式化 imports
refactor: 拆分 trainer 模块为 base 与 callbacks
test: 为 prompt builder 添加边界用例
chore: 添加 .gitignore 并配置远程仓库
perf: 用向量化替换循环计算 attention mask
```

### 不合规示例

```
Add new feature           # 缺少 type 前缀
feat:新增功能。            # 缺少空格，句末多加句号
build: 升级 webpack       # 本项目未启用 build 类型
update files              # 信息模糊、缺少 type
FEAT: 大写的 type          # type 必须小写
```

### 可选 body 与 footer

对于较复杂改动可附 body 说明动机与影响（subject 后空一行）：

```
refactor: 重构 trainer 模块为可插拔结构

将原本耦合在 Trainer 类中的 evaluation / checkpoint 逻辑
抽取为独立的 Callback 子类，便于后续按实验自由组合。

BREAKING CHANGE: Trainer.run() 的回调注册方式变更
```

---

## 2. 分支策略

| 分支 | 用途 | 生命周期 |
| :--- | :--- | :--- |
| `main` | 可发布的主分支 | 永久 |
| `feature/<功能名>` | 新功能开发 | 完成后合并并删除 |
| `bugfix/<问题描述>` | Bug 修复 | 完成后合并并删除 |

分支命名使用 **kebab-case**，必要时可前缀 issue 编号：

```
feature/intent-classifier
feature/12-add-data-augmentation
bugfix/tokenizer-cjk
```

合并策略：

- feature 同步 main：`git rebase main`
- feature 合并回 main：`git merge --no-ff`（保留分支历史）

---

## 3. 日常工作流

```bash
# 1. 同步主分支
git checkout main && git pull origin main

# 2. 创建功能分支
git checkout -b feature/intent-classifier

# 3. 开发、提交（遵循上述格式）
git add <file>
git commit -m "feat: 新增意图分类基线模型"

# 4. 推送
git push -u origin feature/intent-classifier

# 5. 在 GitHub 发起 Pull Request 并合并

# 6. 清理分支
git branch -d feature/intent-classifier
git push origin -d feature/intent-classifier
```

---

## 4. 提交前检查清单

- [ ] type 在 8 种允许类型范围内
- [ ] type 与冒号之间无空格，冒号后有 1 个空格
- [ ] 中文说明清晰具体，无「update」「修改一下」等模糊措辞
- [ ] 单次提交聚焦一个逻辑变更，避免混合无关修改
- [ ] 未提交 secrets / API key / `.env` 等敏感信息
- [ ] 已通过本地基础验证（脚本能运行 / 测试通过）

---

## 5. 远程仓库信息

远程仓库信息通过 `git remote -v` 命令获取，无需手动维护。如需查看当前配置：

```bash
git remote -v
# origin  git@github.com:xiaowenhao404/Grad-School-Agent.git (fetch)
# origin  git@github.com:xiaowenhao404/Grad-School-Agent.git (push)
```

---

## 6. 触发规则

以下场景自动调用本 skill：

- 在 Grad-School-Agent 仓库内执行 `git commit -m ...` 之前
- 提到 commit、提交、git push、写 commit message、rebase、合并分支等关键词
- 询问本项目的 Git 规范或远程仓库地址

如有变更（例如新增 type 或调整分支策略），同时更新本文件与项目 `README.md`。
