# Tailscale Azure Pipelines template

This Azure Pipelines template connects to your [Tailscale network](https://tailscale.com)
by adding a step to your workflow.


```yaml
resources:
    repositories:
      - repository: ado-tailscale
        endpoint: michaeljones32
        name: michaeljones32/ado-tailscale
        type: github
stages:
  - stage: stageName
    jobs:
    - job: jobName
      steps:
      - template: tailscaleTemplate.yml@ado-tailscale
        parameters:
          oauth_client_id: yourOauthClientId
          oauth_secret: yourOauthClientSecret
          version: versionNumber
          tags: "tag:ci"
```

Subsequent steps in the Pipeline can then access nodes in your Tailnet.

oauth_client_id and oauth_secret are an [OAuth client](https://tailscale.com/s/oauth-clients/)
for the tailnet to be accessed. I recommend storing these in [ADO variable groups](https://learn.microsoft.com/en-us/azure/devops/pipelines/library/variable-groups?view=azure-devops&tabs=yaml) as secrets

tags is a comma-separated list of one or more [ACL Tags](https://tailscale.com/kb/1068/acl-tags/)
for the node. At least one tag is required: an OAuth client is not associated
with any of the Users on the tailnet, it has to Tag its nodes.

Nodes created by this Action are [marked as Ephemeral](https://tailscale.com/s/ephemeral-nodes) to
be automatically removed by the coordination server a short time after they
finish their run. The nodes are also [marked Preapproved](https://tailscale.com/kb/1085/auth-keys/)
on tailnets which use [Device Approval](https://tailscale.com/kb/1099/device-approval/)
