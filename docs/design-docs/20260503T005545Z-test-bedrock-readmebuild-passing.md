# 設計書 (Design Document)

## 1. 概要

本設計書は、`okamyuji/prd-design-implementation-agent` リポジトリの `README.md` に、GitHub Actions のワークフロー状態を示す「build-passing」バッジを追加する実装の詳細を記述します。PRDで定義された要件に基づき、具体的な実装方法、テスト計画、品質保証の観点を明確にします。

## 2. システムアーキテクチャ

既存のシステムアーキテクチャに変更はありません。`README.md` ファイルの更新のみを行います。

## 3. コンポーネント設計

### 3.1. README.md

* `README.md` ファイルの冒頭に、Markdown形式でバッジの画像リンクとURLリンクを記述します。

### 3.2. GitHub Actions Workflow

* 既存のGitHub Actions ワークフロー（例: `ci.yml`）の状態を `shields.io` が参照できるように設定します。
* 具体的には、`shields.io` のURLパスに、リポジトリ名、ブランチ名、ワークフローファイル名を指定します。

### 3.3. shields.io

* GitHub Actions のワークフロー状態を監視し、SVG形式のバッジ画像を生成・提供する外部サービスです。

## 4. データ設計

データ構造の変更はありません。

## 5. インターフェース設計

### 5.1. `README.md` への追加内容

```markdown
[![GitHub Workflow Status](https://img.shields.io/github/actions/workflow/status/okamyuji/prd-design-implementation-agent/ci.yml?branch=main&label=build&logo=github&style=flat-square)](https://github.com/okamyuji/prd-design-implementation-agent/actions/workflows/ci.yml)
```

* **画像URL**: `https://img.shields.io/github/actions/workflow/status/okamyuji/prd-design-implementation-agent/ci.yml?branch=main&label=build&logo=github&style=flat-square`
    * `okamyuji/prd-design-implementation-agent`: ターゲットリポジトリ
    * `ci.yml`: 監視対象のワークフローファイル名 (例: `.github/workflows/ci.yml`)
    * `branch=main`: 監視対象のブランチ
    * `label=build`: バッジに表示されるラベル
    * `logo=github`: GitHubロゴを表示
    * `style=flat-square`: バッジのスタイル
* **リンクURL**: `https://github.com/okamyuji/prd-design-implementation-agent/actions/workflows/ci.yml`
    * バッジクリック時に遷移するGitHub Actionsのワークフロー実行ページ

## 6. ロジックツリー

```mermaid
graph TD
    A[README.mdにビルドバッジを追加] --> B{バッジの表示位置決定}
    B --> B1[README.mdの冒頭に配置]

    A --> C{バッジの生成方法決定}
    C --> C1[shields.ioを利用]

    C1 --> D{shields.ioのURL構成要素}
    D --> D1[GitHub Actions Workflow Statusエンドポイント]
    D --> D2[リポジトリオーナー/名: okamyuji/prd-design-implementation-agent]
    D --> D3[ワークフローファイル名: ci.yml]
    D --> D4[ブランチ名: main]
    D --> D5[表示ラベル: build]
    D --> D6[ロゴ: github]
    D --> D7[スタイル: flat-square]

    A --> E{バッジのリンク先決定}
    E --> E1[GitHub Actionsのワークフロー実行ページ]
    E1 --> E2[URL: https://github.com/okamyuji/prd-design-implementation-agent/actions/workflows/ci.yml]

    A --> F{実装}
    F --> F1[README.mdを編集し、Markdownスニペットを挿入]

    A --> G{テスト}
    G --> G1[ローカルでの表示確認]
    G --> G2[PR上での表示確認]
    G --> G3[マージ後の表示確認]
    G --> G4[ワークフロー状態変化時のバッジ更新確認]

    A --> H{品質ゲート}
    H --> H1[Formatter/Linter/静的解析パス]
    H --> H2[テストカバレッジ80%以上 (該当なし、ただし将来的なCI/CD考慮)]
    H --> H3[全テストパス]
    H --> H4[ビルドパス]
```

## 7. テスト計画

### 7.1. テスト戦略

TDD (Test-Driven Development) の原則に従い、変更が期待通りに動作することを確認します。今回はドキュメント変更のため、ユニットテストや結合テストは直接適用されませんが、変更の意図が正しく反映されているかを確認するE2Eテストと手動テストを中心に実施します。

### 7.2. テストケース

#### 7.2.1. 正常系

*   **TC-001**: `README.md` をブラウザで表示した際、冒頭に「build」ラベルのバッジが正しく表示されること。
    *   **期待結果**: バッジが表示され、GitHub Actionsの最新のワークフロー状態（例: `success`）を反映した色（緑）になっている。
*   **TC-002**: バッジをクリックした際、GitHub Actionsの該当ワークフロー実行ページに正しく遷移すること。
    *   **期待結果**: `https://github.com/okamyuji/prd-design-implementation-agent/actions/workflows/ci.yml` に遷移する。
*   **TC-003**: GitHub Actionsのワークフローが成功状態の場合、バッジが緑色で表示されること。
    *   **期待結果**: バッジが緑色で表示される。

#### 7.2.2. 異常系

*   **TC-004**: GitHub Actionsのワークフローが失敗状態の場合、バッジが赤色で表示されること。
    *   **期待結果**: バッジが赤色で表示される。（手動でワークフローを失敗させて確認）
*   **TC-005**: `shields.io` サービスが一時的に利用できない場合でも、`README.md` の他のコンテンツ表示に影響がないこと。
    *   **期待結果**: バッジ画像は表示されないが、`README.md` の他のテキストや画像は正常に表示される。

#### 7.2.3. 境界値・エッジケース

*   **TC-006**: `README.md` の冒頭に他の要素（タイトル、別のバッジなど）がある場合でも、バッジが正しく配置されること。
    *   **期待結果**: 既存のコンテンツと衝突せず、指定された位置にバッジが表示される。
*   **TC-007**: ワークフローが一度も実行されていない場合、バッジが「unknown」などの状態を示すこと。
    *   **期待結果**: `shields.io` のデフォルトの未実行状態が表示される。

#### 7.2.4. E2Eテスト

*   **TC-E2E-001**: PR作成後、GitHubのPRプレビュー画面でバッジが正しく表示され、リンクが機能すること。
    *   **期待結果**: PRプレビューでバッジが表示され、クリックでGitHub Actionsページに遷移する。
*   **TC-E2E-002**: PRがマージされた後、`main` ブランチの `README.md` でバッジが正しく表示され、リンクが機能すること。
    *   **期待結果**: `main` ブランチの `README.md` でバッジが表示され、クリックでGitHub Actionsページに遷移する。

### 7.3. テストカバレッジ

本変更はドキュメントの修正であり、コードの追加・変更を伴わないため、従来のコードカバレッジ計測は適用されません。しかし、将来的なCI/CDパイプラインのテストカバレッジ品質ゲート（80%以上）は維持されるものとします。

## 8. 品質ゲート

以下の条件をすべて満たすことを品質ゲートとします。

*   **Formatter**: `prettier` など、設定されたFormatterが適用され、フォーマットエラーがないこと。
*   **Linter**: `eslint` など、設定されたLinterがパスし、警告・エラーがないこと。
*   **静的解析**: `SonarQube` や `GitHub Code Scanning` など、設定された静的解析ツールがパスし、重大な脆弱性や品質問題が検出されないこと。
*   **テスト**: 上記「7.2. テストケース」のすべてのテストがパスすること。
*   **ビルド**: GitHub ActionsのCIワークフローが成功すること。
*   **カバレッジ**: コード変更がないため直接適用されないが、既存のコードベースのカバレッジが80%以上を維持していること。

## 9. デプロイ計画

1.  開発ブランチ (`feature/add-build-badge`) を作成。
2.  `README.md` を編集し、バッジのMarkdownスニペットを追加。
3.  変更をコミットし、開発ブランチにプッシュ。
4.  GitHubでPRを作成。
5.  PR上で自動テスト（CI）が実行され、品質ゲートを通過することを確認。
6.  レビューアによるコードレビューと承認。
7.  `main` ブランチにマージ。
8.  `main` ブランチへのマージ後、自動デプロイ（GitHub Pagesなど）があれば、それが実行され、公開された `README.md` にバッジが表示されることを確認。

## 10. ロールバック計画

*   万が一問題が発生した場合、`main` ブランチから問題のコミットをリバートすることで、容易に元の状態に戻すことが可能です。

## 11. 変更履歴

| 日付       | バージョン | 変更内容     | 著者     |
| :--------- | :------- | :----------- | :------- |
| 2023-10-27 | 1.0.0    | 新規作成     | Yuji Okami |


---

## Automation Metadata

- Requested at: 2026-05-03T00:55:45.169Z
- Target repository: okamyuji/prd-design-implementation-agent
- Base branch: main
- Working branch: codex/20260503T005545Z-test-bedrock-readmebuild-passing


---

## Generated Artifacts

- PRD Google Doc: https://docs.google.com/document/d/14_JecH9E57cUVHo3U-RoxccwA_OZhNUMfXNxtOt2nWQ/edit
- DesignDoc Google Doc: https://docs.google.com/document/d/1x2M2nVpRTEla1cdr-R_lxql75h5R4ymIzyW9KQeEC5Q/edit
