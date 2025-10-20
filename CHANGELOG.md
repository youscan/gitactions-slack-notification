# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
[markdownlint](https://dlaa.me/markdownlint/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [3.0.0] - 2025-10-20

### Changed

- **BREAKING**: Migrated from Docker Hub to GitHub Container Registry (GHCR)
- **BREAKING**: Docker image now at `ghcr.io/youscan/gitactions-slack-notification`
- **BREAKING**: Action reference changed from `bryannice/gitactions-slack-notification` to `youscan/gitactions-slack-notification`
- Updated workflows to use `$GITHUB_OUTPUT` instead of deprecated `::set-output` commands
- Removed dependency on Docker Hub credentials (now uses `GITHUB_TOKEN`)
- Updated GitHub Actions dependencies to latest versions
- Added Dependabot configuration for automated dependency updates

## [2.0.0] - 2021-01-10

### Added to 2.0.0

- Updated to use news alpine version 3.12
- Updated golang to 1.15.6
- Modify variable naming from SLACK_WEBHOOK to SLACK_INCOMING_WEBHOOK 
- Added work flow build logic to push to docker hub