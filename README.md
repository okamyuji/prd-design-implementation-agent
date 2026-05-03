# prd-design-implementation-agent

> 最終実装エージェントはBedrock LambdaでありCodex Cloudには依存しません。

PRD・DesignDocをもとに、対象リポジトリへ自動的に実装変更を加えるエージェントシステムです。

## 概要

このリポジトリは、プロダクト要求仕様書 (PRD) とデザインドキュメント (DesignDoc) を入力として受け取り、対象リポジトリに対して最小限かつ的確な実装変更を自動的に行うエージェントの実装を含みます。

## アーキテクチャ

- **実装エージェント**: AWS Bedrock Lambda
- **ワークフロー管理**: GitHub Actions
- **ドキュメント生成**: Google Docs API

## 使い方

1. PRD を作成する
2. DesignDoc を作成する
3. エージェントを起動する
4. 生成されたPRをレビュー・マージする

## ライセンス

MIT
