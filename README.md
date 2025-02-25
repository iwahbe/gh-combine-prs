# gh-combine-prs

This is an extension for [GitHub CLI](https://cli.github.com/) that combines multiple PRs into one.
It is intended for use in repositories that receive many PRs that can be merged simultaneously, e.g. trivial Dependabot version bump PRs.

The tool will attempt to create one PR that contains all PRs that:

* match a provided query - e.g. `--query "author:app/dependabot"` so that only Dependabot PRs are processed
* and have checks passing
* and that can be merged cleanly - e.g. if two combinable PRs conflict with one another, only one will be merged.

This tool does not automerge into the `master`/`main` branch - it just attempts to create one unified PR for review and automated checks to run against.

*Note: When you merge the combined PR, it is recommended that you create a Merge Commit.
This allows GitHub to automatically detect that all of the original combined PRs have been merged, so that their state can be set correctly.*

## Installation

Prerequisites:
 * [GitHub CLI](https://cli.github.com/) is already installed and authenticated
 * [`jq`](https://stedolan.github.io/jq/) is installed

To install this extension:

```
gh extension install rnorth/gh-combine-prs
```

## Usage

```shell
cd $DIRECTORY_OF_YOUR_REPO

gh combine-prs --query "QUERY"
```

To auto-merge all dependabot PRs with the added label `impact/no-changelog-required`, run:

``` shell
gh combine-prs --skip-pr-check --query author:app/dependabot --auto-merge --non-interactive --labels impact/no-changelog-required
```

### Required arguments
    --query "QUERY"
            sets the query used to find combinable PRs.
            e.g. --query "author:app/dependabot"
            to combine Dependabot PRs

### Optional arguments
    --selected-pr-numbers COMMA,SEPARATED,LIST
            if set, will only work on PRs with the selected numbers.
            e.g. --selected-pr-numbers 42,13,78
            Defaults to selecting every PR matching the QUERY
    --limit LIMIT
            sets the maximum number of PRs that will be combined.
            Defaults to 50
    --skip-pr-check
            if set, will combine matching PRs even if they are not passing checks.
            Defaults to false when not specified
    --non-interactive
            if set, will not prompt for confirmation before combining PRs. Warning: Can be destructive as we force push to combined-pr-branch to achieve this.
            Defaults to false when not specified
    --auto-merge
            if set, the created PR will be marked to auto-merge.
            Defaults to false when not specified
    --labels
            if set, these labels will be added to the default labels. Labels should be a comma seperated list of labels.

## License

See [LICENSE](./LICENSE)
