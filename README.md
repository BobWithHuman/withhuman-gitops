# withhuman-gitops

What each WithHuman environment runs. Argo CD installs the chart from
[withhuman-core](https://github.com/BobWithHuman/withhuman-core) with the values here.

```text
apps/           Argo CD Applications
envs/<env>/     values of each environment
```

No secret belongs here. Each namespace needs the `withhuman` Secret described in the chart's README.

## Deploying

- **staging** follows `main` of withhuman-core: the Service Images workflow commits each new
  `imageTag`, and Argo CD rolls it out.
- **prod** changes by pull request: copy a tag that passed staging into `envs/prod/values.yaml`, then
  sync `withhuman-prod` in Argo CD after reviewing the diff.

Roll back by reverting the commit that set the tag.

## Setting up Argo CD

1. Register this repository with a read-only deploy key:

   ```sh
   kubectl -n argocd create secret generic withhuman-gitops \
     --from-literal=type=git \
     --from-literal=url=git@github.com:BobWithHuman/withhuman-gitops.git \
     --from-file=sshPrivateKey=argocd-gitops
   kubectl -n argocd label secret withhuman-gitops argocd.argoproj.io/secret-type=repository
   ```

2. Fill in the environment's values, then apply its Application:

   ```sh
   kubectl apply -f apps/staging.yaml
   ```
