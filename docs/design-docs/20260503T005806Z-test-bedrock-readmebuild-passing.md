# 設計ドキュメント

## 1. 概要

### 1.1. プロジェクト名
[TEST-Bedrock] READMEにbuild-passingバッジを追加する

### 1.2. 目的
`okamyuji/prd-design-implementation-agent`リポジトリの`README.md`に、GitHub Actionsのワークフロー状態を示す「build-passing」バッジを追加するための技術設計を定義します。これにより、実装者は本ドキュメントに基づいて機械的に実装を進めることができます。

### 1.3. ゴール
- `README.md`の冒頭に、GitHub Actionsのワークフロー状態を示すshields.io形式の「build-passing」バッジが追加されること。
- バッジが正しくビルド状態を反映し、クリック時に対応するGitHub Actionsのワークフローページへ遷移すること。
- 既存のREADMEコンテンツに影響を与えず、差分が最小限に抑えられていること。

## 2. システムアーキテクチャ

### 2.1. 既存システム概要
対象リポジトリはGitHub上でホストされており、`README.md`はMarkdown形式で記述されています。GitHub ActionsがCI/CDワークフローを実行しています。

### 2.2. 変更点
`README.md`ファイルのみが変更対象となります。外部サービスとしてshields.ioを利用しますが、直接的なシステム連携は発生せず、Markdownの画像埋め込み機能を通じて間接的に利用します。

## 3. 詳細設計

### 3.1. ロジックツリー

```mermaid
graph TD
    A[READMEにビルドバッジを追加] --> B{バッジの表示形式決定}
    B --> B1[shields.io利用]
    B1 --> B1.1[バッジURL生成]
    B1.1 --> B1.1.1[GitHub Actionsワークフロー指定]
    B1.1.1 --> B1.1.1.1[リポジトリ名: okamyuji/prd-design-implementation-agent]
    B1.1.1.1 --> B1.1.1.2[ブランチ名: main]
    B1.1.1.2 --> B1.1.1.3[ワークフローファイル名: ci.yml (仮定)]
    B1.1.1 --> B1.1.2[バッジスタイル指定: build-passing]
    B1.1.2 --> B1.1.2.1[ラベル: build]
    B1.1.2.1 --> B1.1.2.2[スタイル: flat-square (推奨)]
    B --> C{バッジのリンク先決定}
    C --> C1[GitHub Actionsワークフローページ]
    C1 --> C1.1[ワークフローURL生成]
    C1.1 --> C1.1.1[リポジトリ名: okamyuji/prd-design-implementation-agent]
    C1.1.1 --> C1.1.1.2[ワークフローファイル名: ci.yml (仮定)]
    A --> D[README.mdへの追加]
    D --> D1[ファイル冒頭に挿入]
    D1 --> D1.1[Markdown画像リンク形式]
    D1.1 --> D1.1.1[画像URL: B1.1で生成したURL]
    D1.1.1 --> D1.1.1.1[リンクURL: C1.1で生成したURL]
```

### 3.2. 実装詳細

#### 3.2.1. バッジURLの生成
GitHub Actionsのワークフロー状態を示すshields.ioバッジのURLは以下の形式で生成します。

- **リポジトリ**: `okamyuji/prd-design-implementation-agent`
- **ブランチ**: `main`
- **ワークフローファイル名**: `ci.yml` (これは仮定です。実際のリポジトリでCIを実行しているワークフローファイル名を確認してください。もし複数のワークフローがある場合は、主要なCIワークフローを選択します。)

生成されるバッジURLの例:
`https://img.shields.io/github/actions/workflow/status/okamyuji/prd-design-implementation-agent/ci.yml?branch=main&label=build&style=flat-square`

- `label=build`: バッジに表示されるテキストラベル
- `style=flat-square`: バッジのスタイル（推奨）

#### 3.2.2. リンクURLの生成
バッジをクリックした際に遷移するGitHub Actionsのワークフロー実行ページのURLは以下の形式で生成します。

- **リポジトリ**: `okamyuji/prd-design-implementation-agent`
- **ワークフローファイル名**: `ci.yml` (バッジURL生成時と同じワークフローファイル名)

生成されるリンクURLの例:
`https://github.com/okamyuji/prd-design-implementation-agent/actions/workflows/ci.yml`

#### 3.2.3. `README.md`への追加
`README.md`ファイルの**最上部**に、以下のMarkdown形式でバッジを追加します。

```markdown
[![build](https://img.shields.io/github/actions/workflow/status/okamyuji/prd-design-implementation-agent/ci.yml?branch=main&label=build&style=flat-square)](https://github.com/okamyuji/prd-design-implementation-agent/actions/workflows/ci.yml)

# (既存のREADMEタイトルなど)
```

**注意**: 既存のREADMEコンテンツの先頭に挿入するため、既存のタイトル行などがある場合は、その直前に挿入します。

## 4. テスト計画

### 4.1. TDD (Test-Driven Development) の進め方
本タスクはUI変更が主であり、直接的なコードロジックの変更は少ないため、TDDの適用は限定的になります。しかし、以下の観点で「テストファースト」の考え方を適用します。

1. **期待される結果の定義**: PRDとDesignDocで定義されたバッジの表示とリンク機能が、実装後にどのように見えるべきかを明確に定義します。
2. **手動テストケースの作成**: 実装前に、バッジが正しく表示され、リンクが機能することを確認するための具体的な手動テスト手順を作成します。
3. **実装**: 上記テストケースをパスするように`README.md`を修正します。
4. **テスト実行**: 手動テストケースを実行し、期待される結果と一致することを確認します。
5. **リファクタリング**: 不要な変更がないか、Markdownの記述が適切かを確認します。

### 4.2. テスト観点とテストケース

#### 4.2.1. 正常系
- **観点**: バッジが正しく表示され、リンクが機能すること。
- **テストケース**:
    - `README.md`をGitHub上で表示した際、最上部にビルドステータスバッジが表示されること。
    - バッジのラベルが「build」と表示されていること。
    - バッジのスタイルが「flat-square」であること。
    - バッジの色が、最新の`main`ブランチのCIワークフローの成功状態（緑色）を反映していること。
    - バッジをクリックすると、`okamyuji/prd-design-implementation-agent`リポジトリの`ci.yml`ワークフローの実行履歴ページに正しく遷移すること。

#### 4.2.2. 異常系
- **観点**: CIワークフローが失敗した場合のバッジ表示。
- **テストケース**:
    - (手動テストが困難なため、概念的な確認) `main`ブランチのCIワークフローが意図的に失敗した場合、バッジの色が赤色に変化すること。

#### 4.2.3. 境界値・エッジケース
- **観点**: READMEのフォーマットや他の要素との干渉がないこと。
- **テストケース**:
    - バッジ追加後も、既存のREADMEコンテンツ（タイトル、セクション、画像、リンクなど）が正しく表示され、レイアウトが崩れていないこと。
    - バッジの直後に既存のタイトルがある場合でも、改行が適切に処理され、視覚的に問題がないこと。

#### 4.2.4. E2E (End-to-End) テスト
- **観点**: GitHub上での最終的な表示と動作の確認。
- **テストケース**:
    - PRがマージされた後、`main`ブランチの`README.md`がGitHub上で公開された際に、バッジが期待通りに表示され、リンクが機能すること。

### 4.3. 品質ゲート

- **カバレッジ**: 本変更はドキュメント修正のため、コードカバレッジの直接的な適用はありません。ただし、将来的なコード変更を含むPRでは、**テストカバレッジ80%以上**を必須とします。
- **Formatter**: `prettier`などのFormatterが設定されている場合、`README.md`のMarkdownフォーマットがFormatterのルールに準拠していること。
- **Linter**: Markdown Linterが設定されている場合、Linterの警告・エラーがないこと。
- **静的解析**: 静的解析ツールが設定されている場合、警告・エラーがないこと。
- **テスト**: 上記4.2で定義された手動テストケースがすべてパスすること。
- **ビルド**: GitHub ActionsのCIワークフローが成功すること（README変更自体はビルドに影響しないが、PRの品質ゲートとしてCIパスは必須）。

## 5. 運用・保守

- **監視**: 特になし。
- **トラブルシューティング**: バッジが表示されない、またはリンクが機能しない場合は、shields.ioのURL、GitHub Actionsのワークフローファイル名、ブランチ名が正しいかを確認する。
- **更新**: GitHub Actionsのワークフロー名やリポジトリ名が変更された場合は、`README.md`内のバッジURLとリンクURLを更新する必要がある。

## 6. 変更履歴

| 日付       | バージョン | 変更内容         | 著者     |
| :--------- | :--------- | :--------------- | :------- |
| 2023-10-27 | 1.0        | 初版作成         | Yuji Okami |


---

## Automation Metadata

- Requested at: 2026-05-03T00:58:06.873Z
- Target repository: okamyuji/prd-design-implementation-agent
- Base branch: main
- Working branch: codex/20260503T005806Z-test-bedrock-readmebuild-passing


---

## Generated Artifacts

- PRD Google Doc: https://docs.google.com/document/d/1vp5W05L9Y-6GGCu15jbfHa-yILKpPRclf-YlZVUFe8A/edit
- DesignDoc Google Doc: https://docs.google.com/document/d/1RojXKnpb0GSna0jOZhGmGowKIakUlXRlyhVERIxP_GM/edit
