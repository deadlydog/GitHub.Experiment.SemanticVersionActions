# GitHub Experiment of Semantic Version Actions

This is just a simple repository that I'm using to test out various semantic versioning GitHub Actions to see which one I like best.

The [.github/workflows](.github/workflows/) directory contains the workflows I've tested.
Each workflow tests a single action, and is named after the action is uses for the test.

## Requirements

The following are things I'm looking for in the GitHub Actions being tested:

- Supports semantic versioning.
- Supports creating prerelease versions as well (e.g. `1.0.0-alpha.1`).
- Automatically retrieves the current version from the repository; do not have to search tags manually.
- Does not require committing version files (or other files) to the repository for it to work.
- Does not require installing other dependencies (in the repo or the workflow) to work.
- Supports easily specifying a version number manually.

## Results

Only 2 of the actions tested met all of the requirements above.

The winner of the actions tested was [Git Version](https://github.com/marketplace/actions/git-version).
It is not perfect, but it suited my needs the best.
[View the workflow here](.github/workflows/GitVersion-UsesTag.yml).

In a close second was [GitHub Tag Bump](https://github.com/marketplace/actions/github-tag-bump).
It mainly lost because Git Version's prerelease version format is nicer and always unique by default.
[View the workflow here](.github/workflows/GitHubTagBump-UsesTag.yml).

### [Git Version](https://github.com/marketplace/actions/git-version)

Pros:

- Meets all of the requirements above.
- Generates very nice unique prerelease versions: {MAJOR}.{MINOR}.{PATCH}-{sanitized-branch-name}.{commits-distance}.{hash}
- Branches without any changes pushed to them yet will get a unique prerelease version.

Cons:

- Must include `fetch-depth: 0` in checkout step for it to be able to determine the latest version.
  This will cause the checkout step to take longer, which may be problematic for very large repos.
- If using a 'v' prefix on your tags, you need to manually trim it off to get the version number when versioning your assemblies.
- Have to manually parse out the stable version from the prerelease version to use it to version .NET assemblies.

### [GitHub Tag Bump](https://github.com/marketplace/actions/github-tag-bump)

Pros:

- Meets all of the requirements above.
- Supports creating the new tag on the repo, so you don't have to find another action to do that.

Cons:

- Does not use a prerelease version when on another branch that does not yet have any commits.
  If your CI/CD process attempts a deployment, it will conflict with the main branch's deployed version.
- Different branches will all end up with the same prerelease version if you do not provide a `PRERELEASE_SUFFIX` parameter that uniquely identifies the branch (e.g. the commit SHA).
- If using a 'v' prefix on your tags, you need to manually trim it off to get the version number when versioning your assemblies.
- Have to manually parse out the stable version from the prerelease version to use it to version .NET assemblies.

### [Easy Versioning](https://github.com/marketplace/actions/easy-versioning)

Cons:

- Does not support tags.
  You would have to read the tags yourself to find the latest version and then pass the version into the action.
  Because of this, it was not considered further.

### [Increment Version](https://github.com/marketplace/actions/increment-version)

- Does not support tags.
  You would have to read the tags yourself to find the latest version and then pass the version into the action.
  Because of this, it was not considered further.

### [Version Bump](https://github.com/marketplace/actions/version-bump)

- Does not support creating prerelease versions.
  Version is always blank on non-main branch runs.
  Because of this, it was not considered further.

### [Version Generator](https://github.com/marketplace/actions/version-generator)

- Does not support creating prerelease versions.
  Version is always blank on non-main branch runs.
  Because of this, it was not considered further.

## Other information

[This blog post](https://medium.com/@AranT/auto-tagging-and-using-semantic-versioning-with-github-actions-e40188d12cf4) was helpful and led me to discover the GitHub Tag Bump action.
