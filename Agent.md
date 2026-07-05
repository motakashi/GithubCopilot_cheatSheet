# Agent.skillsの利用について
 - https://github.com/anthropics/skills
 - https://github.com/github/awesome-copilot
 - https://github.com/microsoft/skills
 - https://www.skills.sh/ （一部危険なものもあるので注意）

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
