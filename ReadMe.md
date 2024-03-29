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

Only 3 of the actions tested met all of the requirements above.

1st place: I personally like [GitTools](https://github.com/marketplace/actions/gittools) the best, simply because it provides the most outputs for the version number, meaning you do not have to do any extra work to parse them out.
If you do not like one of the default version number formats, you can create your own using the provided outputs.

2nd place: My next favourite is [Git Version](https://github.com/marketplace/actions/git-version).
I actually prefer it's version number format best, but if you want to use the 'v' prefix or use it to tag .NET assemblies, you need to do extra work to parse out just the version number parts you need.

3rd place: My third pick is [GitHub Tag Bump](https://github.com/marketplace/actions/github-tag-bump).
It mainly lost because Git Version's prerelease version format is nicer and always unique by default.

### [GitTools](https://github.com/marketplace/actions/gittools)

[View the workflow](.github/workflows/GitTools-UsesTag.yml) and the [workflow runs](https://github.com/deadlydog/GitHub.Experiment.SemanticVersionActions/actions/workflows/GitTools-UsesTag.yml).

Sample `SemVer` output (there are a few other outputs as well):

- PR: `1.1.15-PullRequest0009.4`
- Branch: `1.1.15-TestBranch.1`
- Main: `1.1.15`

Pros:

- Meets all of the requirements above.
- Has many different outputs for the version, such as `Major`, `Minor`, `Patch`, `PreReleaseTag`, `PreReleaseTagWithDash`, `MajorMinorPatch`, `SemVer`, `AssemblySemVer`, `Sha`, `ShortSha`, `NuGetVersion`, `CommitsSinceVersionSource`, `CommitDate`, and more.
- Branches without any changes pushed to them yet will get a unique prerelease version.
- Detects tags that start with `v`, even though it does not output them for any version number formats.

Cons:

- Must include `fetch-depth: 0` in checkout step for it to be able to determine the latest version.
  This will cause the checkout step to take longer, which may be problematic for very large repos.
- Must include an additional setup step to install GitVersion.

### [Git Version](https://github.com/marketplace/actions/git-version)

[View the workflow](.github/workflows/GitVersion-UsesTag.yml) and the [workflow runs](https://github.com/deadlydog/GitHub.Experiment.SemanticVersionActions/actions/workflows/GitVersion-UsesTag.yml).

Sample output (with optional 'v' prefix added):

- PR: `v1.1.15-v11144gfc3c6c9.4.fc3c6c9`
- Branch: `v1.1.15-testbranch.5.7856217`
- Main: `v1.1.15`

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

[View the workflow](.github/workflows/GitHubTagBump-UsesTag.yml) and the [workflow runs](https://github.com/deadlydog/GitHub.Experiment.SemanticVersionActions/actions/workflows/GitHubTagBump-UsesTag.yml).

Sample output (with optional 'v' prefix added):

- PR: `v1.1.15-fc3c6c9f72fe8fa5d827b676eb6d976782816d21.0`
- Branch: `v1.1.15-785621799c5a4450f8159bb87f20d06493d6beb0.0`
- Main: `v1.1.15`

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

[View the workflow](.github/workflows/EasyVersioning.yml) and the [workflow runs](https://github.com/deadlydog/GitHub.Experiment.SemanticVersionActions/actions/workflows/EasyVersioning.yml).

- Does not support tags.
  You would have to read the tags yourself to find the latest version and then pass the version into the action.
  Because of this, it was not considered further.

### [Increment Version](https://github.com/marketplace/actions/increment-version)

[View the workflow](.github/workflows/IncrementVersion.yml) and the [workflow runs](https://github.com/deadlydog/GitHub.Experiment.SemanticVersionActions/actions/workflows/IncrementVersion.yml).

- Does not support tags.
  You would have to read the tags yourself to find the latest version and then pass the version into the action.
  Because of this, it was not considered further.

### [Version Bump](https://github.com/marketplace/actions/version-bump)

[View the workflow](.github/workflows/VersionBump-UsesTag.yml) and the [workflow runs](https://github.com/deadlydog/GitHub.Experiment.SemanticVersionActions/actions/workflows/VersionBump-UsesTag.yml).

- Does not support creating prerelease versions.
  Version is always blank on non-main branch runs.
  Because of this, it was not considered further.

### [Version Generator](https://github.com/marketplace/actions/version-generator)

[View the workflow](.github/workflows/VersionGenerator-UsesTag.yml) and the [workflow runs](https://github.com/deadlydog/GitHub.Experiment.SemanticVersionActions/actions/workflows/VersionGenerator-UsesTag.yml).

- Does not support creating prerelease versions.
  Version is always blank on non-main branch runs.
  Because of this, it was not considered further.

## Other information

[This blog post](https://medium.com/@AranT/auto-tagging-and-using-semantic-versioning-with-github-actions-e40188d12cf4) was helpful and led me to discover the GitHub Tag Bump action.
