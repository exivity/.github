# .github

Exivity meta repository for GitHub settings

## Organization profile

The `profile/README.md` will appear on the
[organization's profile](https://github.com/exivity).

## Workflow templates

To add one of these example workflows to a repository: go to the _Actions_ tab, then
click on the _New workflow_ button and scroll down the _By Exivity_ section.

### [`build`](https://github.com/exivity/.github/blob/main/workflow-templates/go.yml)

Example build workflow for Go repositories. Modify where needed to support repositories
in different languages.

### [`accept`](https://github.com/exivity/.github/blob/main/workflow-templates/accept.yml)

In order to run the acceptance tests (on scaffold) before merging changes to the
development/release branches, we can use the [accept action from the actions
repository](https://github.com/exivity/actions#accept). There are two approaches
to using this action:

**Run on request**  
This is the recommended approach. The acceptance tests will run under these
conditions:
- If commit is part of a pull requests, require review request from 
  [@exivity-bot](https://github.com/exivity-bot)
- If commit is not on the `master` or `main` branch

In practice, we run the tests on the `develop` branch (if there is one) and in
pull requests after requesting a review from _@exivity-bot_.

It requires a separate _accept_ workflow. See example workflow in
[workflow-templates/accept.yml](https://github.com/exivity/.github/blob/main/workflow-templates/accept.yml)

_Note: because it runs as a separate workflow, we need to specify the workflow
where artefacts are generated. In the_ accept _workflow, set the value of 
`on.workflow_run.workflows` to the workflow_ name _(!) of the artefact-generating
workflow. Due to a GitHub limitation, also make sure the_ job _in the build
workflow has the same name to prevent problems with detecting the workflow status._

```yaml
  workflow_run:
    workflows: ['build']
```

**Run on each commit**  
This is the most straight-forward approach and you can include the action in
your build workflow:

```yaml
name: build
on: push
jobs:
  build:
    steps:
      # ...
      - uses: exivity/actions/accept@main
        with:
          gh-token: ${{ secrets.GH_BOT_TOKEN }}
```

This is not very efficient, however, as the acceptance tests take ~30mins to
complete and if you push a lot of commits, you might want some more control over
when the tests run. This is also the expensive option as the platform running
the tests is a metered service.

### [`virustotal-check`](https://github.com/exivity/.github/blob/main/workflow-templates/virustotal-check.yml)

VirusTotal check workflow for the [virustotal](https://github.com/exivity/actions#virustotal) action.

### [`semantic-pull-request`](https://github.com/exivity/.github/blob/main/workflow-templates/semantic-pull-request.yml)

Workflow to check for semantic PR titles using the [semantic-pull-request](https://github.com/exivity/actions#semantic-pull-request) action.

### [`purge-ghcr`](https://github.com/exivity/.github/blob/main/workflow-templates/purge-ghcr.yml)

Purge the GitHub Container Registry when a branch or tag is deleted.
Uses the [purge-ghcr](https://github.com/exivity/actions#purge-ghcr) action.

### [`sync-defaults`](https://github.com/exivity/.github/blob/main/workflow-templates/sync-defaults.yml)

Repositories in the Exivity organisation can use the
[sync-defaults](https://github.com/exivity/actions#sync-defaults) action to sync
repository settings with the [default settings for Exivity](https://github.com/exivity/.github/blob/main/.github/settings.yml).
Setup this workflow to sync periodically and when making changes to the local
overrides file `.github/settings.yml`.
