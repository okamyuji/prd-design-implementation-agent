# prd-design-implementation-agent

A GitHub App that automatically generates PRD (Product Requirements Document) and Design Document from issues, then implements the changes using AI.

## Features

- Automatically generates PRD and Design Document from GitHub issues
- Implements changes based on PRD and Design Document using AI
- Creates pull requests with the implemented changes

## Setup

### Prerequisites

- Node.js 20+
- A GitHub App with the following permissions:
  - Repository: Read & Write (Contents, Pull requests, Issues)
  - Organization: Read (Members)
- Google Cloud Project with the following APIs enabled:
  - Vertex AI API
  - Google Docs API
  - Google Drive API

### Environment Variables

```bash
APP_ID=your_github_app_id
PRIVATE_KEY=your_github_app_private_key
WEBHOOK_SECRET=your_webhook_secret
GOOGLE_CLOUD_PROJECT=your_gcp_project_id
GOOGLE_CLOUD_LOCATION=us-central1
```

### Installation

```bash
npm install
npm run build
npm start
```

## Usage

1. Create a GitHub issue with a description of the feature or change you want to implement.
2. The app will automatically generate a PRD and Design Document from the issue.
3. The app will implement the changes based on the PRD and Design Document.
4. A pull request will be created with the implemented changes.

## Development

```bash
npm run dev
```

## License

MIT

このリポジトリの自動レビューはCodeRabbitとGemini Code Assistで適応します。