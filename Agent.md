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
