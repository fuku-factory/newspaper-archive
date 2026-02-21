# newspaper-archive

[newspaper-generator](https://github.com/fuku-factory/newspaper-generator) で収集・生成されたデータの長期保存用アーカイブリポジトリ。

## 目的

newspaper-generator 本体リポジトリの容量を制御するため、保持期間（デフォルト14日）を超過したスナップショット・出力データを本リポジトリに自動移行する。GitHub Actions のアーカイブワークフローにより週1回自動実行される。

- 本体リポジトリ: コード＋直近データに集中（GitHubリポジトリ推奨上限1GB以内）
- 本リポジトリ: 古いデータの永続保存（長期分析・動画生成連携用）

## ディレクトリ構造

本体リポジトリと同一のディレクトリ構造を維持する。

```
newspaper-archive/
├── data/
│   └── snapshots/
│       └── {config_name}/
│           └── YYYY-MM-DDTHHMMSSZ.json
└── output/
    └── {config_name}/
        └── YYYY-MM-DD/
            ├── index.html
            └── data.json
```

### スナップショット (`data/snapshots/`)

YouTube Data API等から収集した生データのスナップショット。

- **ファイル名**: ISO 8601形式のUTCタイムスタンプ（例: `2026-02-15T120000Z.json`）
- **形式**: JSON
- **内容**: 収集時刻、設定ハッシュ、動画データ配列

### 出力 (`output/`)

変動検知・AI分析結果から生成されたダッシュボード。

- **ディレクトリ名**: 日付（`YYYY-MM-DD`）
- **ファイル**: `index.html`（ダッシュボードHTML）、`data.json`（動画連携用JSON）

### config名によるサブディレクトリ分離

カテゴリ別の設定ファイル（`configs/politics.yaml`等）に対応し、config名でサブディレクトリが分離される。

- `configs/politics.yaml` → `data/snapshots/politics/`, `output/politics/`
- `configs/economy.yaml` → `data/snapshots/economy/`, `output/economy/`

## データ方針

- 本リポジトリにはデータファイルのみを格納する（アプリケーションコードは含めない）
- データの自動削除は行わない（長期分析用に永続保存）
- アーカイブ元の情報はコミットメッセージに記録される
