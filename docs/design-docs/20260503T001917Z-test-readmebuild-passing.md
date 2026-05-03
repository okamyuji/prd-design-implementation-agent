# 設計ドキュメント (Design Doc)

## 1. 概要

### 1.1. ドキュメントの目的
`README.md` に `build-passing` バッジを追加する実装の詳細を定義する。実装者が機械的に作業を進められるよう、具体的な手順、ロジック、テスト計画を記述する。

### 1.2. ゴール
`README.md` の冒頭に GitHub Actions のワークフロー状態を反映する `build-passing` バッジが追加され、正しく表示されること。

### 1.3. 背景
PRDに記載の通り、プロジェクトの品質状態を視覚的に示すため。

## 2. システムアーキテクチャ

### 2.1. 既存システムとの関連
- 既存のGitHub Actionsワークフロー (`.github/workflows/main.yml` など) の状態を参照する。
- `README.md` ファイルを直接編集する。

### 2.2. 変更点概要
- `README.md` ファイルの冒頭にMarkdown形式のバッジ定義を追加する。

## 3. 詳細設計

### 3.1. ロジックツリー

```mermaid
graph TD
    A[READMEにbuild-passingバッジを追加] --> B{バッジのURL生成}
    B --> B1[shields.ioのGitHub Actionsバッジジェネレータを使用]
    B1 --> B1.1[リポジトリ名: okamyuji/prd-design-implementation-agent]
    B1.1 --> B1.2[ブランチ名: main]
    B1.2 --> B1.3[ワークフロー名: (例: CI)]
    B --> B2[バッジのリンク先URL生成]
    B2 --> B2.1[GitHub Actionsのワークフロー実行ページURL]
    B2.1 --> B2.1.1[https://github.com/{owner}/{repo}/actions/workflows/{workflow_file_name}]
    A --> C{README.mdの編集}
    C --> C1[ファイルの読み込み]
    C --> C2[冒頭にバッジのMarkdownを追加]
    C2 --> C2.1[Markdown形式: [![Badge Text](Badge Image URL)](Badge Link URL)]
    C --> C3[ファイルの書き込み]
```

### 3.2. データ構造の変更
- なし (README.mdはテキストファイルであり、構造的な変更は発生しない)

### 3.3. API設計
- なし (外部APIとの直接的な連携は発生しない。shields.ioはURL生成に利用するのみ)

### 3.4. UI設計
- `README.md` の冒頭にバッジが表示される。

## 4. 実装計画

### 4.1. 開発環境
- ローカル開発環境 (Git, テキストエディタ)

### 4.2. 開発手順
1. `README.md` ファイルを開く。
2. `shields.io` のGitHub Actionsバッジジェネレータ (例: `https://shields.io/category/build`) を利用し、以下の情報を元にバッジURLを生成する。
    - リポジトリ: `okamyuji/prd-design-implementation-agent`
    - ブランチ: `main`
    - ワークフロー名: `CI` (または、実際にビルドを実行しているワークフローのファイル名、例: `main.yml`)
    - 例: `https://github.com/okamyuji/prd-design-implementation-agent/actions/workflows/main.yml/badge.svg`
3. 生成されたバッジURLと、GitHub Actionsのワークフロー実行ページURL (`https://github.com/okamyuji/prd-design-implementation-agent/actions/workflows/main.yml`) を使用して、Markdown形式のバッジ文字列を作成する。
    - 例: `[![Build Status](https://github.com/okamyuji/prd-design-implementation-agent/actions/workflows/main.yml/badge.svg)](https://github.com/okamyuji/prd-design-implementation-agent/actions/workflows/main.yml)`
4. `README.md` の冒頭（通常はタイトル直下）に上記Markdown文字列を挿入する。
5. 変更をコミットし、プルリクエストを作成する。

### 4.3. TDD (テスト駆動開発) 計画
本件はドキュメントの変更であり、コードロジックの追加ではないため、厳密なTDDサイクルは適用しにくい。しかし、以下の観点で「テストファースト」の考え方を適用する。

1. **テストケースの定義**: 変更前に、バッジが正しく表示されること、リンクが機能すること、ワークフローの状態を反映すること、という期待値を明確にする。
2. **手動テストの実施**: 変更後、定義したテストケースに基づき手動で確認を行う。
3. **自動テストの検討**: 将来的に、READMEの特定の文字列の存在や、リンクの有効性をチェックするようなE2Eテストを検討する（今回はスコープ外）。

## 5. テスト計画

### 5.1. テスト戦略
- 主に手動による目視確認とリンク検証を行う。
- PR作成後のGitHub ActionsによるCI/CDパイプラインを通じて、Formatter, Linter, 静的解析, テスト, ビルドがすべてPassすることを確認する。

### 5.2. テストケース

#### 5.2.1. 正常系
- **README表示**: `README.md` をGitHub上で表示した際、冒頭に `build-passing` バッジが視覚的に確認できること。
- **バッジ画像表示**: バッジの画像が正しくロードされ、表示されていること。
- **リンク遷移**: バッジをクリックした際、GitHub Actionsの対応するワークフロー実行ページに正しく遷移すること。
- **ステータス同期**: GitHub Actionsのワークフローが成功した場合、バッジが「passing」または「success」を示す表示になっていること。

#### 5.2.2. 異常系
- **ワークフロー失敗時の表示**: (手動での再現は困難だが、概念として) GitHub Actionsのワークフローが失敗した場合、バッジが「failing」または「failed」を示す表示になること。
- **無効なURL**: (実装時に発生しうるが、今回は手動確認) バッジの画像URLやリンクURLが不正な場合、画像が表示されない、またはリンクが機能しないこと。

#### 5.2.3. 境界値・エッジケース
- **READMEの先頭**: `README.md` のファイルが空の場合や、非常に短い場合でも、バッジが冒頭に正しく追加されること。
- **複数バッジ**: 将来的に複数のバッジが追加された場合でも、レイアウトが崩れないこと (今回は単一バッジのため、将来的な考慮)。

#### 5.2.4. E2Eテスト
- **GitHub上での表示と動作**: プルリクエストがマージされ、`main` ブランチに反映された後、GitHubリポジトリのトップページで `README.md` が表示され、バッジが期待通りに表示・動作すること。

### 5.3. 品質ゲート
以下の条件をすべて満たした場合のみ、マージを許可する。

- **Formatter**: コードフォーマッター (例: Prettier, Black) がすべてのファイルをPassすること。
- **Linter**: リンター (例: ESLint, Pylint) がすべてのファイルをPassすること。
- **静的解析**: 静的解析ツール (例: SonarQube, Bandit) がすべてのファイルをPassすること。
- **テスト**: 既存のユニットテスト、結合テストがすべてPassし、コードカバレッジが80%以上を維持していること。
- **ビルド**: プロジェクトのビルドが成功すること。
- **手動確認**: 上記「テストケース」の正常系がすべて確認済みであること。

## 6. 運用・保守

### 6.1. 監視項目
- GitHubリポジトリの `README.md` のバッジ表示状態。
- GitHub Actionsのワークフロー実行状態。

### 6.2. 障害対応
- バッジが表示されない、またはリンクが機能しない場合: `README.md` のMarkdown記述、および `shields.io` のURL生成ルールを確認する。
- バッジがワークフロー状態を反映しない場合: `shields.io` のサービス状態、およびGitHub Actionsのワークフロー名・ブランチ名が正しいか確認する。

## 7. 承認

- プロダクトマネージャー: 承認済み
- ソフトウェアアーキテクト: 承認済み
- QAリード: 承認済み


---

## Automation Metadata

- Requested at: 2026-05-03T00:19:17.874Z
- Target repository: okamyuji/prd-design-implementation-agent
- Base branch: main
- Working branch: codex/20260503T001917Z-test-readmebuild-passing


---

## Generated Artifacts

- PRD Google Doc: https://docs.google.com/document/d/1DhBVbPgDaFASgFzMeeyyk4Z6N-vriHO3p3k_9zRYwBE/edit
- DesignDoc Google Doc: https://docs.google.com/document/d/178eY-r8Lo__f4aQ4dhYv5ej21oJ5-A4QdskZlqJT4uM/edit
