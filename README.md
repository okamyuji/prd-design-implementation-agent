# prd-design-implementation-agent

An automated agent that transforms PRDs and Design Docs into repository changes via pull requests.

## Overview

This repository contains an AI-powered implementation agent that:

1. Accepts a PRD (Product Requirements Document) and Design Doc as input
2. Analyzes the target repository structure
3. Generates a file change plan
4. Creates a pull request with the proposed changes

## Usage

The agent is triggered via GitHub Actions workflow dispatch with the following inputs:

- `target_repository`: The repository to modify
- `prd_markdown`: The PRD content in Markdown format
- `design_doc_markdown`: The Design Doc content in Markdown format

## Workflow

1. **Input Processing**: Parse PRD and Design Doc
2. **Repository Analysis**: Examine existing code structure and conventions
3. **Plan Generation**: Create a JSON file change plan using AI
4. **PR Creation**: Apply changes and open a pull request

## Security

The agent includes the following security measures:

- **Path Validation**: Ensures all file operations are within the repository boundary
- **Masking**: Sensitive values (tokens, secrets) are masked in logs
- **Branch Protection**: Changes are always made via pull requests, never directly to protected branches

## Smoke Test - 2026-05-03

本リポジトリのセキュリティ改修（パス検証、マスキング、ブランチ保護）は、2026-05-03時点で動作確認済みです。
