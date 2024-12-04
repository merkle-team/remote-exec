# warpcast/remote-exec

Executes a `stack` command on a remote host.

Allows us to reuse this logic across all applications we deploy via [Stack](https://github.com/warpcast/stack).

## Inputs
- `git-repo` **(required)** GitHub repository to clone (e.g. `my-org/my-repo-name`)
- `git-ref` **(required)** Commit ref to check out (typically a hash)
- `command` **(required)** one of `deploy` or `plan`
- `project` **(required)** the project name as specified in the `deploy.yml` configuration
- `ssh-key` **(required)** private SSH key to use to connect to `ssh-user`@`ssh-host`
- `ssh-user` _(optional)_ the user to connect to the `ssh-host` as (default `ec2-user`)
- `ssh-host` _(optional)_ the remote host to SSH into (default `stack.warpcast.com`)
- `release-id` _(optional)_ name of the release (default timestamp + commit hash of the form `2024-12-31T23-59-59-123Z-deadbeef`)

## How to use

Invoke in your workflow using:

```yaml
jobs:
  my-job:
    steps:
      - uses: warpcast/remote-exec
        with:
          git-repo: ${{ github.repository }}
          git-ref: ${{ github.sha }}
          command: make deploy # Assumes your repo has a Makefile with this defined
          project: 'my-project'
          ssh-key: ${{ secrets.STACK_DEPLOY_SSH_PRIVATE_KEY }}
```

Notice that there's no need to clone the repo before invoking this action.
Cloning occurs remotely on the host itself.
This action assumes that only trusted code is being executed by the time it is run.
Thus it is **NOT** recommended to use this action as part of a pull request, but rather as part of a release workflow that runs post-merge.
