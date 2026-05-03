# prd-design-implementation-agent

AI-powered agent that automatically generates PRD, Design Doc, and implementation from a task title.

## Overview

This repository provides an automated pipeline that:
1. Accepts a task title as input
2. Generates a Product Requirements Document (PRD) using AI
3. Creates a Design Document based on the PRD
4. Produces implementation changes guided by both documents

## Features

- **PRD Generation**: Automatically creates structured product requirements
- **Design Doc Generation**: Translates requirements into technical design
- **Implementation Planning**: Generates file change plans as structured JSON
- **Google Docs Integration**: Saves generated documents to Google Docs
- **GitHub Integration**: Creates branches and pull requests automatically

## Screenshots

### Workflow Trigger
![Workflow Trigger](docs/images/workflow-trigger.png)

### Generated PRD
![Generated PRD](docs/images/generated-prd.png)

### Pull Request
![Pull Request](docs/images/pull-request.png)

## Usage

1. Navigate to **Actions** tab in the repository
2. Select **PRD → DesignDoc → Implementation Agent** workflow
3. Click **Run workflow**
4. Enter a task title in the input field
5. Click **Run workflow** to start the pipeline

## Requirements

- GitHub Actions enabled
- OpenAI API key configured as repository secret (`OPENAI_API_KEY`)
- Google service account credentials for Docs integration
- GitHub token with appropriate permissions

## Configuration

Required secrets:
- `OPENAI_API_KEY`: OpenAI API key for AI generation
- `GOOGLE_SERVICE_ACCOUNT_JSON`: Google service account for Docs API
- `GH_PAT`: GitHub Personal Access Token

最終動作確認: 2026-05-03
