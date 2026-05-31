# SKILLS

GitHub Copilot の SKILLS は、特定タスクでエージェントの精度を上げるための
「命令 + 補助リソース（例、スクリプト）」をまとめたフォルダです。

タスク実行時に Copilot が関連性を判断し、必要な場合のみ SKILL.md の内容をコンテキストに取り込みます。
そのため、常に有効にしたい汎用ルールは「カスタム命令」、特定タスク向けの詳細手順は「SKILLS」に分けるのが実践的です。

## 配置場所

プロジェクトで使う場合（リポジトリ単位）

```text
.github/skills/<skill-name>/SKILL.md
```

または

```text
.claude/skills/<skill-name>/SKILL.md
```

または

```text
.agents/skills/<skill-name>/SKILL.md
```

個人で使う場合（複数プロジェクト共有）

```text
~/.copilot/skills/<skill-name>/SKILL.md
```

または

```text
~/.agents/skills/<skill-name>/SKILL.md
```

補足:
- skill-name は小文字 + ハイフン区切りが推奨
- ファイル名は必ず SKILL.md

## SKILL.md の最小構成

```md
---
name: github-actions-failure-debugging
description: Guide for debugging failing GitHub Actions workflows. Use this when asked to debug failing GitHub Actions workflows.
---

When asked to debug failing GitHub Actions workflows, follow this process:
1. Check recent workflow runs.
2. Summarize failure logs.
3. Reproduce locally if needed.
4. Fix and verify.
```

## スクリプト同梱の例

```text
.github/skills/image-convert/
├── SKILL.md
└── convert-svg-to-png.sh
```

SKILL.md の本文で、どのタイミングでどの引数でスクリプトを実行するかを明示します。

## allowed-tools の注意

```md
---
name: image-convert
description: Converts SVG images to PNG format.
allowed-tools: shell
---
```

allowed-tools で shell / bash を事前許可すると確認ステップを減らせますが、
安全性の観点で信頼できるスキルに限定してください。
不明なスキルは事前許可せず、都度確認フローで運用するのが安全です。

## 既存スキルの導入・管理

GitHub CLI の gh skill サブコマンドで、検索・プレビュー・インストール・更新が可能です。

```bash
gh skill search TOPIC
gh skill preview OWNER/REPOSITORY SKILL
gh skill install OWNER/REPOSITORY SKILL
gh skill update
```

## 参考

- https://docs.github.com/ja/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills
