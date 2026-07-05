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
└── skills/
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

# Skillsのbody
`SKILL.md` の frontmatter より下は、Skill の本体です。ここには「この Skill が何を受け取り、どう処理し、何を返すか」を人にもモデルにも分かる形で記述します。

## bodyでよく書く項目

### `steps`
Skill の実行手順や判断フローを書きます。

- 何から始めるか
- どの順番で処理するか
- 条件分岐がある場合はどう振る舞うか
- 失敗時や情報不足時にどうするか

抽象的に「調べる」「整理する」と書くより、できるだけ順序立てて具体的に書くのが重要です。

例:

```markdown
## Steps
1. ユーザーの依頼内容から対象リポジトリと目的を特定する
2. 主要ディレクトリと設定ファイルを確認する
3. 実装の中心となるファイルを3〜5個抽出する
4. 各ファイルの役割を要約する
5. 最後に全体構成と注意点を箇条書きでまとめる
```

### `input`
Skill が想定する入力を明記します。

- ユーザーが何を渡すのか
- 必須入力と任意入力は何か
- 入力形式は自然文か、URLか、ファイルパスか
- 前提条件はあるか

例:

```markdown
## Input
- リポジトリ名、またはリポジトリURL
- 調査対象のディレクトリやファイル名（任意）
- 知りたい観点（例: 構成、依存関係、改善点）
```

### `output`
Skill がどのような出力を返すかを書きます。

- 箇条書きで返すのか
- 表形式にするのか
- サマリと詳細を分けるのか
- ファイルパスや根拠を含めるのか

出力形式を明示しておくと、回答の品質が安定しやすくなります。

例:

```markdown
## Output
- 3〜5行の全体サマリ
- 主要ファイル一覧と役割
- 注意点や追加調査が必要な点
```

### `examples`
想定される入力と出力の例を書きます。

- 典型的な依頼例
- 境界ケースに近い例
- 望ましい返答の粒度

例があると、Skill の使い方と期待値が伝わりやすくなります。

例:

```markdown
## Examples
### Example 1
Input:
- owner/repo の認証周りを要約して

Output:
- 認証は `src/auth/` 配下に集約されている
- `middleware.ts` でトークン検証を行う
- `session.ts` でセッションの生成と破棄を扱う
```

### `edge cases`
通常ケース以外の扱いも書いておくと、運用しやすくなります。

- 入力が不足している場合
- 対象が大きすぎる場合
- 曖昧な依頼の場合
- 複数解釈がありうる場合
- ファイルが存在しない場合

例:

```markdown
## Edge Cases
- リポジトリ名が指定されていない場合は、まず対象確認を行う
- 対象範囲が広すぎる場合は、主要ディレクトリに絞って要約する
- 指定ファイルが見つからない場合は、近い候補を提示する
```

## bodyは具体的に書く

body は短くてもよいですが、曖昧すぎると Skill として機能しにくくなります。

悪い例:

```markdown
このSkillはコードを読んで整理する。
```

良い例:

```markdown
このSkillは、指定されたリポジトリまたはディレクトリを調査し、主要ファイル、依存関係、責務分担、注意点を箇条書きで整理する。対象が広い場合は、まずトップレベル構成を確認してから中核ディレクトリに絞って要約する。
```

具体的に書くときの観点:

- 対象: 何を調べる Skill なのか
- 条件: どんな時に使うのか
- 手順: 何をどの順で行うのか
- 出力: 何を返すのか
- 例外: うまくいかない時にどうするのか

## 500文字以上になる場合の考え方

body が長くなりすぎる場合は、全部を `SKILL.md` に詰め込まず、補助ファイルへ分割して参照するのが管理しやすいです。

特に次のような場合は分割を検討します。

- 手順が多く、説明が長い
- 例が複数必要
- ドメイン知識や用語集が多い
- 長いテンプレート文やチェックリストを含む

### 分割先の例

- `reference/` : 用語、背景知識、仕様メモ
- `examples/` : 入出力例
- `templates/` : 長いテンプレートや定型文
- `scripts/` : 実行補助や検証スクリプト

例:

```text
skills/
└── my-skill/
    ├── SKILL.md
    ├── reference/
    │   └── domain-notes.md
    ├── examples/
    │   ├── basic.md
    │   └── advanced.md
    └── templates/
        └── output-format.md
```

### `SKILL.md` 側では要点だけを書く

`SKILL.md` には Skill の核となる説明を残し、詳細は補助ファイルに逃がすと見通しがよくなります。

例:

```markdown
## Steps
1. 対象を特定する
2. 主要ファイルを確認する
3. 結果を要約する

詳細な観点や用語は `reference/domain-notes.md` を参照。
追加の出力例は `examples/basic.md` と `examples/advanced.md` を参照。
```

## body作成のコツ

- frontmatter は簡潔に、body は具体的に書く
- 長文を1か所に詰め込まず、役割ごとにファイルを分割する
- `steps`、`input`、`output`、`examples`、`edge cases` を意識すると整理しやすい
- まず最小構成で作り、使いながら補助ファイルを増やすと保守しやすい

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
