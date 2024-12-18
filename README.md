# warpcast/remote-exec

Executes a `stack` command on a remote host.

Allows us to reuse this logic across all applications we deploy via [Stack](https://github.com/warpcast/stack).

## Inputs
- `git-repo` **(required)** GitHub repository to clone (e.g. `my-org/my-repo-name`)
- `git-ref` **(required)** Commit ref to check out (typically a hash)
- `docker-image` **(required)** Full URL to the Docker image to pull on deployed instances
- `command` **(required)** one of `deploy` or `plan`
- `project` **(required)** the project name as specified in the `deploy.yml` configuration
- `role` **(required)** ARN of the role to assume in AWS (assumes you already have OIDC set up)
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
          docker-image: 123456789012.dkr.ecr.us-east-1.amazonaws.com/my-organization/my-repository:${{ github.sha }} # ECR repo
          command: deploy
          project: 'my-project'
          role: arn:aws:iam::123456789012:role/my-role-that-allows-oidc-auth
```

Notice that there's no need to clone the repo before invoking this action.
Cloning occurs remotely on the host itself.
This action assumes that only trusted code is being executed by the time it is run.
Thus it is **NOT** recommended to use this action as part of a pull request, but rather as part of a release workflow that runs post-merge.
