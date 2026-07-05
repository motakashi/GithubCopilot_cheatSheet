# Agent.skillsの利用について
 - https://github.com/anthropics/skills
 - https://github.com/github/awesome-copilot
 - https://github.com/microsoft/skills
 - https://www.skills.sh/ （一部危険なものもあるので注意）

# Skillsの配置場所
## プロジェクト（リポジトリ）で管理する場合

プロジェクト固有の Skill は、リポジトリ内に `skills/` ディレクトリを作って管理すると分かりやすいです。

```text
<repository-root>/
├── skills/
│   └── my-skill/
│       └── SKILL.md
├── src/
└── README.md
```

- チームで共有しやすい
- リポジトリ固有の文脈や運用ルールを Skill に反映しやすい
- アプリやライブラリと一緒にバージョン管理できる

## 個人（ホームディレクトリ）で管理する場合

複数のプロジェクトで使い回したい Skill は、ホームディレクトリ配下にまとめて置く運用が便利です。

```text
~/.config/
└─��� skills/
    └── my-skill/
        └── SKILL.md
```

または、環境によっては次のように自分用ディレクトリを切って管理してもよいです。

```text
~/skills/
└── my-skill/
    └── SKILL.md
```

- 個人用の定番 Skill を横断的に再利用しやすい
- 特定リポジトリに依存しない知識をまとめやすい
- 自分専用のテンプレートや補助スクリプトを育てやすい

## 使い分けの目安

- リポジトリ固有の前提、設計、運用ルールに強く依存するものは **プロジェクト配下**
- 複数リポジトリで共通利用したいものは **ホームディレクトリ配下**
- 迷ったら、最初はリポジトリ配下に置き、汎用化できたら個人用 Skill として切り出すと整理しやすい

# Skillsのfrontmatter
Skill の `SKILL.md` では、先頭に YAML frontmatter を書いて Skill の識別情報や挙動を定義します。

## 記載例

````markdown
---
name: my-skill
description: このSkillの目的、何をするか、どんな時に使うかを説明する
license: MIT
compatibility:
  - github-copilot
  - claude-code
user-invocable: true
disable-model-invocation: false
---

# Skill Title

ここから下に Skill 本文を書く
````

## 主な項目

| 項目 | 内容 | 記載例 | 必須 |
|---|---|---|---|
| `name` | Skill の一意な識別子。英数字とハイフン中心で、短く分かりやすい名前にする | `name: my-skill` | ○ |
| `description` | Skill が何をするか、どんな場面で使うかを書く説明文 | `description: リポジトリの構成を調査して要点を整理するSkill` | ○ |
| `license` | Skill のライセンス情報。公開・共有前提なら明記すると扱いやすい | `license: MIT` | △ |
| `compatibility` | どの実行環境・ツール系での利用を想定するかを示す情報 | `compatibility:\n  - github-copilot\n  - claude-code` | △ |
| `user-invocable` | ユーザーが明示的にコマンドや slash command で呼び出せるか | `user-invocable: true` | △ |
| `disable-model-invocation` | モデル側による自動起動を抑止するか。`true` なら明示呼び出し専用にしやすい | `disable-model-invocation: true` | △ |

## 項目ごとの補足

### `name`
- 基本的に必須項目
- Skill を識別するための名前
- できるだけ用途が伝わる名前にする
- 英数字とハイフンで揃えると扱いやすい

例:

```yaml
name: repo-review
```

### `description`
- 基本的に必須項目
- 「何をする Skill か」だけでなく「どんな時に使うか」まで書くと分かりやすい
- 自動選択される仕組みがある環境では特に重要

例:

```yaml
description: リポジトリの構成、主要ファイル、依存関係を調査して概要をまとめる
```

### `license`
- 必須ではないことが多いが、共有配布するなら記載推奨
- OSS ライセンス名をシンプルに書く形が分かりやすい

例:

```yaml
license: MIT
```

### `compatibility`
- 必須ではないことが多い
- どのツールや実装を想定している Skill か示したい時に有用
- 配列形式で書いておくと拡張しやすい

例:

```yaml
compatibility:
  - github-copilot
  - claude-code
```

### `user-invocable`
- 必須ではないことが多い
- `true` の場合、ユーザーが明示的に呼び出す運用に向く
- 公開コマンドとして見せたい Skill で使いやすい

例:

```yaml
user-invocable: true
```

### `disable-model-invocation`
- 必須ではないことが多い
- `true` にすると、モデルが自動では使わず、ユーザーや明示フローからの起動を前提にしやすい
- 誤発火を避けたい Skill や、重い Skill に向く

例:

```yaml
disable-model-invocation: true
```

## 使い分けの例

- 普段から候補として自動利用されてもよい Skill
  - `user-invocable: true`
  - `disable-model-invocation: false`
- 明示的にだけ使いたい Skill
  - `user-invocable: true`
  - `disable-model-invocation: true`
- ユーザーから直接は見せず、内部利用寄りにしたい Skill
  - `user-invocable: false`
  - `disable-model-invocation: false`

## 注意

- 実際に使える frontmatter 項目は、Skill を読むツールや実装ごとに差があります
- `name` と `description` は広く基本項目として扱われやすいです
- `license`、`compatibility`、`user-invocable`、`disable-model-invocation` は実装依存の差分が出やすいため、利用先の仕様確認が必要です

# Skillsの基本構成
## 最小構成

```text
skills/
└── my-skill/
    ├── SKILL.md
    ├── reference/          # 任意: 参照資料
    │   └── glossary.md
    ├── scripts/            # 任意: 補助スクリプト
    │   └── setup.sh
    └── assets/             # 任意: 画像や補助ファイル
        └── example.png
```

- `skills/` : Skills をまとめるルートディレクトリ
- `my-skill/` : 個別 Skill ごとのディレクトリ
- `SKILL.md` : Skill の本体。目的、使い方、前提条件、手順などを記述する中心ファイル
- `reference/` : 用語集、仕様メモ、参考資料などを置く任意ディレクトリ
- `scripts/` : セットアップ、検証、補助処理などのスクリプトを置く任意ディレクトリ
- `assets/` : 画像や補助資料を置く任意ディレクトリ

## 実用的な構成例

```text
skills/
└── my-skill/
    ├── SKILL.md
    ├── reference/
    │   ├── glossary.md
    │   └── links.md
    ├── scripts/
    │   ├── setup.sh
    │   └── validate.sh
    ├── examples/
    │   ├── input.md
    │   └── output.md
    ├── templates/
    │   └── prompt.md
    └── assets/
        └── diagram.png
```

- `reference/` : 参照用の知識や外部リンク整理に向く
- `scripts/` : 繰り返し使う補助処理をまとめられる
- `examples/` : 入出力例やユースケースを置く
- `templates/` : 再利用するテンプレートや定型文を置く
- `assets/` : 図や画像など説明補助の素材を置く

## 補足

- まずは `SKILL.md` だけでも Skill として成立することが多い
- `reference/` や `scripts/` は必要になってから追加してよい
- 補助ファイルが増えてきたら、役割ごとに分けると管理しやすい
- Skill 名は用途が分かるディレクトリ名にすると再利用しやすい
