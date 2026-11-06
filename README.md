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

Terraform in withhuman-core (`infra/`) installs Argo CD and applies the cluster's Application from
`apps/`, so nothing here is applied by hand. This repository is public, and Argo CD reads it over
https without credentials.

Before a first sync, fill in the environment's `neo4j` URLs and `ingress.host` from the Terraform
outputs.
