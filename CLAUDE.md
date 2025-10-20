# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a GitHub Action that sends Slack notifications about workflow statuses. It's written in Go and packaged as a Docker container action. The action reads GitHub workflow context via environment variables and formats them into rich Slack messages using the Incoming Webhooks API.

## Build and Development Commands

### Building the Docker image
```bash
make docker-build
```
This builds the Docker image using a multi-stage build with Go 1.15.6 and Alpine 3.12.

### Formatting Go code
```bash
make fmt
```
Runs `gofmt` on the `command/notifier` and `configuration` packages.

### Test build (local Go build)
```bash
make test-build
```
Formats code and builds the Go binary locally without Docker.

### Building with custom parameters
```bash
make DOCKER_IMAGE_NAME=custom-name GIT_VERSION=1.0.0 docker-build
```

## Architecture

### Package Structure

- **`main.go`**: Entry point that orchestrates configuration initialization and message sending
- **`configuration/`**: Handles environment variable parsing and validation
  - `configuration.go`: Defines `Config` struct and `Init()` method that reads required/optional env vars
- **`command/notifier/`**: Slack messaging logic
  - `message.go`: Defines Slack message data structures (Message, Attachment, Field)
  - `notifier.go`: Implements `Send()` method that POSTs JSON to Slack webhook

### Message Flow

1. `main.go` initializes `Config` from environment variables
2. Constructs a `Message` with GitHub context (actor, ref, event, repository)
3. Calls `Message.Send()` which marshals to JSON and POSTs to Slack webhook URL
4. Message includes author info from GitHub actor and links to the repository actions page

### Required Environment Variables

The action requires these three environment variables to function:
- `SLACK_INCOMING_WEBHOOK`: The Slack webhook URL
- `SLACK_TITLE`: Title for the Slack message
- `SLACK_MESSAGE`: The message content

All other environment variables (GitHub context, Slack customization) are optional and handled in `configuration/configuration.go:47-57`.

## Docker Build Process

The multi-stage Dockerfile (`build/docker/Dockerfile`) uses:
1. **Stage 1**: golang:1.15.6-alpine3.12 to compile the Go binary
2. **Stage 2**: alpine:3.12 as minimal runtime, copies binary and runs as non-root user `gitactions-slack-notification`

## GitHub Actions Workflows

- **`.github/workflows/ci_branch_build.yml`**: Runs on non-master branches. Builds Docker image via Make, then tests it by sending an actual Slack notification.
- **`.github/workflows/cd_build_package.yml`**: Runs on version tags (e.g., `1.0.0`). Builds and pushes to Docker Hub with both `latest` and version-specific tags.

## Usage as a GitHub Action

The action is defined in `action.yml` and uses the published Docker image from Docker Hub. See `deployment/git-actions/template_slack_notification.yml` for a complete example with all available environment variables.

The action constructs messages with GitHub metadata automatically, creating a formatted Slack attachment that shows:
- Author (GitHub actor) with profile picture
- Git ref and event type
- Link to repository actions page
- Custom title and message from environment variables
