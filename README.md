# Protolint Action

This action runs [protolint](https://github.com/yoheimuta/protolint) using the
[action-protolint](https://github.com/yoheimuta/action-protolint) GitHub
Action with a custom configuration used in most Frequenz projects.

Here is an example demonstrating how to use it in a workflow:

```yaml
jobs:
  protolint:
    name: Check proto files with protolint
    runs-on: ubuntu-20.04

    steps:
      - name: Fetch sources
        uses: actions/checkout@v4
      - name: Run protolint
        uses: frequenz-floss/gh-action-protolint@v0.x.x
```

By default, the action will checkout submodules (non-recursively) and use the
`github.token` to do GitHub API calls to comment in PRs for example. It
looks for proto files in the `proto/` directory and uses a fixed protolint
version (please look at the [`action.yml`](./action.yml) file for the current
version).

You can change those defaults using custom inputs. For example:

```yaml
jobs:
  protolint:
    name: Check proto files with protolint
    runs-on: ubuntu-20.04

    steps:
      - name: Fetch sources
        uses: actions/checkout@v4
      - name: Run protolint
        uses: frequenz-floss/gh-action-protolint@v0.x.x
        with:
          github_token: ${{ secrets.my_api_token }}
          protolint_flags: "-config_path=.github/protolint.yaml proto/"
          protolint_version: "v0.46.0"
```

## Security

`protolint` reads its configuration from the checked-out repository. In a
`pull_request_target` workflow this action is only safe as long as the checkout
uses the default event ref, which resolves to the base branch.

Do not use this action with `pull_request_target` if your workflow checks out
untrusted PR code or otherwise overrides the checkout ref to the PR head. In
that case, a PR can provide a malicious `.protolint.yaml` that changes how
`protolint` runs.
