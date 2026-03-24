# dd-mcp-investigator

[English version](README.md)

[Datadog MCP Server](https://docs.datadoghq.com/bits_ai/mcp_server/) を活用したインシデント調査用の [Claude Code](https://docs.anthropic.com/en/docs/claude-code) スキルです。Observe（観察）→ Hypothesize（仮説立案）→ Act（検証）のループを繰り返し、根本原因を特定してインタラクティブな HTML レポートを生成します。

> **注**: これは個人プロジェクトであり、Datadog, Inc. とは関係ありません。

## 機能一覧

- Datadog MCP ツール（13カテゴリ・88ツール）を活用した体系的な根本原因分析
- 仮説の生成と検証を繰り返すイテレーティブループ
- 調査フローサイドバーとタブ付き詳細カードを備えたインタラクティブ HTML レポート
- 仮説ステータスの可視化（Validated：検証済み / Inconclusive：未確定 / Invalidated：否定済み）

## 前提条件

### 1. Claude Code

```bash
npm install -g @anthropic-ai/claude-code
```

### 2. Datadog MCP Server

Datadog MCP Server を Claude Code に設定する必要があります。詳細は [Datadog MCP Server セットアップガイド](https://docs.datadoghq.com/bits_ai/mcp_server/setup/) を参照してください。

```bash
# Claude Code 内で接続を確認
/mcp
```

## インストール

### 方法 1: このディレクトリで Claude Code を起動する（推奨）

```bash
cd dd-mcp-investigator
claude
```

`.claude/skills/` 配下のスキルは自動検出されます。`/incident-investigation` で呼び出せます。

### 方法 2: 既存プロジェクトにスキルをコピーする

```bash
cp -r /path/to/dd-mcp-investigator/.claude/skills/incident-investigation \
      /your/project/.claude/skills/incident-investigation
```

### 方法 3: SKILL.md の内容を CLAUDE.md に貼り付ける

軽量に使いたい場合は、`.claude/skills/incident-investigation/SKILL.md` の内容をプロジェクトの `CLAUDE.md` に直接コピーしてください。

## 使い方

### スキルの呼び出し

```
/incident-investigation
```

自然言語でも呼び出せます：

```
Investigate the monitor "CPU Usage High on host:example-host"
What caused INC-12345?
Analyze the latency spike on payment-service
```

### 調査フロー

```
1. ソースの特定
   └─ モニター・インシデント・ホストの詳細を取得

2. 初期観察
   └─ メトリクス、ログ、トレース、ホスト、イベント等を収集

3. 仮説調査（イテレーティブループ）
   ├─ 観察結果から仮説を生成
   ├─ Datadog データで各仮説を検証
   ├─ ステータスを付与（検証済み / 未確定 / 否定済み）
   └─ サブ仮説で深掘り

4. 結論
   └─ 根本原因の特定 + 推奨アクション

5. HTML レポート生成
   └─ インタラクティブな HTML レポートを生成
```

### HTML レポートの構成

| セクション | 説明 |
|---------|-------------|
| **Source カード** | アラート・インシデントの概要（赤アクセントボーダー付き） |
| **Initial observation ピル** | 初期観察のステップ数 |
| **Investigation Flow** | 仮説の親子関係をツリー表示するサイドバー。クリックで詳細にスクロール。 |
| **Hypothesis details** | タブ付きカード（Evidence / Research Steps / Assessment）。サブ仮説は親カード内にネスト。 |
| **Conclusion カード** | 根本原因またはインコンクルーシブな調査結果とアクションアイテム |

### ステータスの凡例

| ステータス | 色 | 意味 |
|--------|-------|---------|
| Validated | 赤 (#E42313) | データにより仮説が確認された |
| Inconclusive | 琥珀 (#D97706) | 確認も否定もできる証拠が不十分 |
| Invalidated | グレー (#B0B0B0) | データにより仮説が否定された |

## ファイル構成

```
dd-mcp-investigator/
├── .claude/
│   └── skills/
│       └── incident-investigation/
│           ├── SKILL.md                        # スキル定義（Claude Code が自動検出）
│           └── templates/
│               └── investigation-report.html   # HTML レポートテンプレート
├── CLAUDE.md                                   # プロジェクト設定
└── README.md
```

## ライセンス

MIT
