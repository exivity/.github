# .github

GitHub defaults

## Workflow templates

### `accept`

In order to run the acceptance tests (on scaffold) before merging changes to the
development/release branches, we can use the [accept action from the actions
repository](https://github.com/exivity/actions#accept). There are two approaches
to using this action:

**Run on request**  
This is the recommended approach. The acceptance tests will run under these
conditions:
- If commit is part of a pull requests, require review request from 
  [@exivity-bot](github.com/exivity-bot)
- If commit is not on the `master` or `main` branch

In practice, we run the tests on the `develop` branch (if there is one) and in
pull requests after requesting a review from _@exivity-bot_.

It requires a separate _accept_ workflow. See example workflow in
[workflow-templates/accept.yml](https://github.com/exivity/.github/blob/main/workflow-templates/accept.yml)

_Note: because it runs as a separate workflow, we need to specify the workflow
where artefacts are generated. In the workflow, change the value of_ build _if
your artefacts are generated in a different workflow:_

```
  workflow_run:
    workflows: [build]
```

**Run on each commit**  
This is the most straight-forward approach and you can include the action in
your build workflow:

```
name: build
on: push
jobs:
  build:
    steps:
      # ...
      - uses: exivity/actions/accept@master
```

This is not very efficient, however, as the acceptance tests take ~30mins to
complete and if you push a lot of commits, you might want some more control over
when the tests run. This is also the expensive option as the platform running
the tests is a metered service.
