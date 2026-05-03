# 設計ドキュメント (Design Doc)

## 1. 概要

### 1.1. プロジェクト名
[TEST-Bedrock] READMEにbuild-passingバッジを追加する

### 1.2. 目的
`okamyuji/prd-design-implementation-agent`リポジトリの`README.md`に、GitHub Actionsのワークフロー状態を示す「build-passing」バッジを追加します。これにより、リポジトリのビルド状態の可視化を向上させ、開発者および利用者が最新のビルド状況を容易に把握できるようにします。

### 1.3. ゴール
- `README.md`の冒頭に、GitHub Actionsの特定のワークフロー（例: `ci.yml`）のステータスを示すshields.io形式のバッジが追加されること。
- バッジがGitHub上で正しくレンダリングされ、機能すること。
- 既存の`README.md`コンテンツに不要な変更を与えないこと。

## 2. システムアーキテクチャ

### 2.1. 既存システム概要
- GitHubリポジトリ `okamyuji/prd-design-implementation-agent`
- `main`ブランチに`README.md`が存在する。
- GitHub ActionsによるCI/CDワークフローが設定されている（本件では既存のワークフローを利用）。

### 2.2. 変更点概要
- `README.md`ファイルのみを変更します。
- GitHub Actionsのワークフロー自体には変更を加えません。

## 3. 詳細設計

### 3.1. ロジックツリー
```mermaid
graph TD
    A[README.mdにビルドバッジを追加する] --> B{バッジの配置場所の決定}
    B --> B1[README.mdの冒頭に配置]
    A --> C{バッジの形式の決定}
    C --> C1[shields.io形式を採用]
    C --> C2[GitHub ActionsのワークフローバッジURLを使用]
    A --> D{対象ワークフローの特定}
    D --> D1[既存のCIワークフロー (例: ci.yml) を対象とする]
    A --> E{Markdown記法の決定}
    E --> E1[画像リンクとURLリンクの組み合わせ]
    E1 --> E1a[画像URL: https://github.com/{owner}/{repo}/actions/workflows/{workflow_file}/badge.svg]
    E1 --> E1b[リンクURL: https://github.com/{owner}/{repo}/actions/workflows/{workflow_file}]
    A --> F{実装と検証}
    F --> F1[README.mdを編集]
    F --> F2[PRを作成し、GitHub上で表示を確認]
    F --> F3[バッジのステータスが正しく反映されるか確認]
    F --> F4[バッジクリックでワークフローページに遷移するか確認]
```

### 3.2. データ構造の変更
- 該当なし。`README.md`はテキストファイルであり、構造的なデータ変更はありません。

### 3.3. APIの変更
- 該当なし。

### 3.4. UI/UXの変更
- `README.md`の冒頭にビルドバッジが追加されます。
- バッジはGitHubのMarkdownレンダリングによって表示されます。

### 3.5. データベースの変更
- 該当なし。

### 3.6. 外部システム連携
- GitHub Actionsのワークフロー状態をshields.io経由で取得し、表示します。

## 4. 実装計画

### 4.1. タスク分解
1. **タスク1: 対象ワークフローの特定**
    - `okamyuji/prd-design-implementation-agent`リポジトリ内のCI/CD関連のGitHub Actionsワークフローファイル（例: `.github/workflows/ci.yml`）を特定する。
    - **成果物**: ワークフローファイル名（例: `ci.yml`）
2. **タスク2: バッジURLの生成**
    - 特定したワークフローファイル名に基づき、shields.io形式のバッジ画像URLと、GitHub Actionsのワークフロー実行ページへのリンクURLを生成する。
    - **成果物**: バッジ画像URL、リンクURL
3. **タスク3: README.mdの編集**
    - `README.md`ファイルの冒頭に、生成したURLを用いてMarkdown形式でバッジを追加する。
    - **成果物**: 変更された`README.md`ファイル
4. **タスク4: プルリクエストの作成とレビュー**
    - 変更をコミットし、プルリクエストを作成する。
    - レビューを依頼し、フィードバックを反映する。
    - **成果物**: マージ可能なプルリクエスト

### 4.2. 開発環境
- ローカル開発環境 (VS Code, Git)
- GitHub (プルリクエスト、GitHub Actions)

### 4.3. テスト計画 (TDDアプローチ)

#### 4.3.1. テストフェーズ
1. **事前準備**: `README.md`の現在の状態を把握する。
2. **テストケース設計**: 以下のテスト観点に基づき、具体的なテストケースを記述する。
3. **実装**: `README.md`にバッジを追加する。
4. **テスト実行**: 設計したテストケースを実行し、期待通りに動作するか確認する。
5. **リファクタリング**: コードの品質を向上させる（本件ではMarkdownの整形など）。

#### 4.3.2. テスト観点と具体的なテストケース
- **正常系**
    - **テストケース**: GitHub ActionsのCIワークフローを成功させるコミットをプッシュし、PRDの「ユーザーシナリオ」に沿ってGitHub上で`README.md`を確認する。
    - **期待結果**: バッジが緑色で「build passing」と表示され、クリックするとCIワークフローの成功した実行ページに遷移する。
- **異常系**
    - **テストケース**: GitHub ActionsのCIワークフローを意図的に失敗させるコミット（例: 存在しないコマンドを実行する）をプッシュし、PRDの「ユーザーシナリオ」に沿ってGitHub上で`README.md`を確認する。
    - **期待結果**: バッジが赤色で「build failing」と表示され、クリックするとCIワークフローの失敗した実行ページに遷移する。
- **境界値/エッジケース**
    - **テストケース**: ワークフローが一度も実行されていない状態（新規ワークフロー追加時など）を想定し、バッジの表示を確認する。
    - **期待結果**: バッジが「no status」または適切なデフォルト表示となること。
    - **テストケース**: ネットワークが不安定な状況をシミュレートし、バッジ画像が読み込めない場合の表示を確認する。
    - **期待結果**: 代替テキスト（例: `GitHub Workflow Status`）が表示されること。
- **E2Eテスト**
    - **テストケース**: プルリクエストを`main`ブランチにマージした後、GitHub上で`main`ブランチの`README.md`にアクセスし、バッジの表示とリンク機能を確認する。
    - **期待結果**: マージされた変更が反映され、バッジが正しく機能すること。

#### 4.3.3. 品質ゲート
- **自動テスト**: 本件ではコード変更がないため、ユニットテスト、結合テストのカバレッジは適用外。
- **静的解析**: Markdown Linter (例: `markdownlint`) を使用し、`README.md`の構文エラーやスタイル違反がないことを確認する。
- **Formatter**: Markdown Formatter (例: `prettier`のMarkdown対応) を使用し、`README.md`が整形されていることを確認する。
- **ビルド**: 該当なし。
- **手動確認**: 上記「テスト観点と具体的なテストケース」のすべてが満たされていることを目視で確認する。
- **レビュー**: 少なくとも1名以上のレビュワーによる承認。

## 5. 運用・保守

### 5.1. 監視
- GitHub Actionsのワークフロー自体がバッジのデータソースであるため、ワークフローの監視がバッジの健全性監視に直結します。

### 5.2. ログ
- 該当なし。

### 5.3. メンテナンス
- ワークフローファイル名が変更された場合、`README.md`内のバッジURLも更新する必要があります。

## 6. セキュリティ

### 6.1. 脆弱性対策
- GitHubが提供する公式のバッジURLを使用するため、直接的な脆弱性リスクは低い。

## 7. 費用

- 該当なし。

## 8. 承認

- プロダクトマネージャー: [氏名] (日付)
- ソフトウェアアーキテクト: [氏名] (日付)
- QAリード: [氏名] (日付)


---

## Automation Metadata

- Requested at: 2026-05-03T00:50:04.891Z
- Target repository: okamyuji/prd-design-implementation-agent
- Base branch: main
- Working branch: codex/20260503T005004Z-test-bedrock-readmebuild-passing


---

## Generated Artifacts

- PRD Google Doc: https://docs.google.com/document/d/1BvdTDlEgin3-Y7Z1G2lXgV1QWCzblnB74wLw6tfSiOk/edit
- DesignDoc Google Doc: https://docs.google.com/document/d/13-P3ZeeULrKZjGyp7sAbvuekbfLJ-aMaI9IJnBG4dvo/edit
