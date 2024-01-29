# GitHub Experiment of Semantic Version Actions

This is just a simple repository that I'm using to test out various semantic versioning GitHub Actions to see which one I like best.

The [.github/workflows](.github/workflows/) directory contains the workflows I've tested.
Each workflow tests a single action, and is named after the action is uses for the test.

## Requirements

The following are things I'm looking for in the GitHub Actions being tested:

- Supports semantic versioning.
- Supports creating prerelease versions as well (e.g. `1.0.0-alpha.1`).
- Does not require committing version files (or other files) to the repository for it to work.
- Ideally, it also makes it easy to update the version manually.

Dummy change 2
