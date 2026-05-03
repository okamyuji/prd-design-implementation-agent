# 設計書 (Design Document)

## 1. 概要

本設計書は、`okamyuji/prd-design-implementation-agent`リポジトリの`README.md`にGitHub Actionsのワークフロー状態を示す「build-passing」バッジを追加する実装の詳細を記述します。PRDで定義された要件に基づき、具体的な実装方法、テスト計画、品質保証の基準を明確にします。

## 2. システムアーキテクチャ

変更は`README.md`ファイルのみに限定され、既存のシステムアーキテクチャへの影響はありません。

## 3. コンポーネント設計

### 3.1. `README.md`の変更

- `README.md`の冒頭にMarkdown形式でバッジの記述を追加します。
- バッジのURLはshields.ioを利用し、GitHub Actionsのワークフロー状態を動的に取得します。

## 4. データモデル

変更なし。

## 5. API設計

変更なし。

## 6. ロジックツリー

```mermaid
graph TD
    A[README.mdにビルドバッジを追加する]
    A --> B{バッジの表示形式を決定する}
    B --> B1[shields.ioを利用する]
    B --> B2[Markdown形式で記述する]
    A --> C{バッジの情報を決定する}
    C --> C1[対象リポジトリ: okamyuji/prd-design-implementation-agent]
    C --> C2[対象ブランチ: main]
    C --> C3[対象ワークフロー: CI/CDワークフロー (例: ci.yml)]
    A --> D{バッジのURLを生成する}
    D --> D1[shields.ioのGitHub Actionsワークフロー状態バッジのURL形式に従う]
    D1 --> D1a[例: https://img.shields.io/github/actions/workflow/status/okamyuji/prd-design-implementation-agent/ci.yml/main?branch=main]
    A --> E{README.mdにバッジを挿入する}
    E --> E1[ファイルの冒頭に挿入する]
    E --> E2[バッジにリンクを設定する (GitHub Actionsのワークフロー実行ページ)]
    A --> F{変更をテストする}
    F --> F1[ローカルでの表示確認]
    F --> F2[PR上での表示確認]
    F --> F3[GitHub Actionsのワークフロー状態との同期確認]
```

## 7. 詳細設計

### 7.1. バッジのURL構成

shields.ioのGitHub Actionsワークフロー状態バッジのURLは以下の形式を使用します。

`https://img.shields.io/github/actions/workflow/status/{owner}/{repo}/{workflow_file_name}/{branch}?branch={branch}`

- `{owner}`: `okamyuji`
- `{repo}`: `prd-design-implementation-agent`
- `{workflow_file_name}`: 対象となるGitHub Actionsワークフローのファイル名（例: `ci.yml`）。本リポジトリのCI/CDワークフローファイル名を確認し、正確な値を設定します。
- `{branch}`: `main`

例: `https://img.shields.io/github/actions/workflow/status/okamyuji/prd-design-implementation-agent/ci.yml/main?branch=main`

### 7.2. `README.md`への記述

`README.md`の冒頭に以下のMarkdownを挿入します。

```markdown
[![Build Status](https://img.shields.io/github/actions/workflow/status/okamyuji/prd-design-implementation-agent/ci.yml/main?branch=main)](https://github.com/okamyuji/prd-design-implementation-agent/actions/workflows/ci.yml)
```

**注記**: `ci.yml`は仮のワークフローファイル名です。実際のリポジトリに存在するCI/CDワークフローファイル名に置き換えてください。

## 8. テスト計画

### 8.1. テスト戦略

TDD (Test-Driven Development) の原則に従い、変更前にテスト観点を明確にし、変更後にテストを実行します。本件はドキュメント変更のため、ユニットテストは不要ですが、表示確認と機能確認を重点的に行います。

### 8.2. テストケース

#### 8.2.1. 正常系

- **TC-001**: `README.md`をブラウザで開いた際、冒頭にビルドバッジが正しく表示されること。
- **TC-002**: ビルドバッジの表示が、GitHub Actionsの最新のワークフロー実行状態（成功/失敗）と一致すること。
- **TC-003**: ビルドバッジをクリックした際、対応するGitHub Actionsのワークフロー実行ページに正しく遷移すること。

#### 8.2.2. 異常系

- **TC-004**: GitHub Actionsのワークフローが失敗した場合、バッジが「failing」などの失敗状態を示す表示に変わること。
- **TC-005**: ワークフローファイル名が誤っている場合、バッジが「not found」などのエラー状態を示す表示になること（shields.ioの挙動に依存）。

#### 8.2.3. 境界値・エッジケース

- **TC-006**: `README.md`が非常に長い場合でも、バッジが冒頭に表示され続けること。
- **TC-007**: GitHub Actionsのワークフローがまだ一度も実行されていない場合、バッジが「no status」などの状態を示すこと（shields.ioの挙動に依存）。

#### 8.2.4. E2Eテスト

- **TC-E2E-001**: PRがマージされ、`main`ブランチに反映された後、GitHubリポジトリのトップページで`README.md`のビルドバッジが正しく機能していることを確認する。

### 8.3. 品質ゲート

以下の条件をすべて満たすことを品質ゲートとします。

- Formatter (Prettierなど) が適用され、コードスタイルが統一されていること。
- Linter (ESLintなど) がすべての警告・エラーを解消していること。
- 静的解析ツール (SonarQubeなど) が重大な問題を検出しないこと。
- テストカバレッジは本件では適用外（ドキュメント変更のため）。
- ビルドが成功すること。
- 上記テストケースがすべてパスすること。

## 9. デプロイ計画

- 開発ブランチで変更を実装し、PRを作成。
- PRレビューとGitHub ActionsによるCIチェックをパス。
- `main`ブランチにマージ。

## 10. ロールバック計画

- 問題が発生した場合、該当PRをリバートする。

## 11. 考慮事項

- 対象となるGitHub Actionsワークフローの正確なファイル名を確認すること。
- `README.md`の既存コンテンツとの競合がないか確認すること。

## 12. 変更履歴

| 日付       | バージョン | 変更内容         | 著者     |
| :--------- | :--------- | :--------------- | :------- |
| 22/07/2024 | 1.0.0      | 新規作成         | Yuji Oka |


---

## Automation Metadata

- Requested at: 2026-05-03T00:47:46.374Z
- Target repository: okamyuji/prd-design-implementation-agent
- Base branch: main
- Working branch: codex/20260503T004746Z-test-bedrock-readmebuild-passing


---

## Generated Artifacts

- PRD Google Doc: https://docs.google.com/document/d/1iGRAZ9dwXYMV6WMatzzNnc2dXrhJm1RPSW2DPcr89UI/edit
- DesignDoc Google Doc: https://docs.google.com/document/d/1eKaEnP7GGsA6KPaWuIGaawHlX3JvZPr8ZV9DKyE168Q/edit
