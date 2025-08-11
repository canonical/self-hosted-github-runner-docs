(private_endpoint_runners)=
# Private endpoint runners user guide

This guide covers how to start using the private-endpoint GitHub self-hosted runners which allow access to Canonical internal resources. For example, the private endpoint runners can allow connection to OpenStack or Juju controllers on PS6 for testing on different architectures only available within the Canonical infrastructure.

The following will be required to start using the private-endpoint runners. Each section is covered in detail below.

* Firewall (private-endpoint-runners \-\> Canonical internal resources)  
* Workflow private-endpoint-runner labels  
* GitHub secrets  
* Repository rules  
* Do’s and Don’ts

## Firewall

To start allowing traffic from the self-hosted private-endpoint GitHub runners to your desired service within Canonical, please set up firewall rules accordingly at [https://code.launchpad.net/canonical-is-firewalls/](https://code.launchpad.net/canonical-is-firewalls/) . Note that granting access here means **anyone in Canonical using the private-endpoint runners will gain access** accordingly.  
The private-endpoint runner service is defined under `services/is/github-runner.yaml production-private-endpoint-runners`.

Example: Access PS6 OpenStack Keystone

```
# rules/is/ps6.yaml

- comment: "Allow private-endpoint runners to access to PS6 keystone"
  from: [services/is/github-runner/production-private-endpoint-runners]
  to: [services/is/ps6/keystone]
  ports: [keystone-auth]
```

Example: Access Vault

```
# rules/is/vault.yaml

- comment: "Allow private-endpoint runners to access to Vault"
  from: [services/is/github-runner/production-private-endpoint-runners]
  to: [services/is/vault/vault]
  ports: [vault]
```

Example: Access Juju

```
# rules/is/juju.yaml

- comment: "Allow private-endpoint runners to access Juju controller"
  from: [services/<team>/<team>-github-runner/<model name>]  to: [services/is/juju/controllers-ps6]  ports: [jujud-api]
```

## GitHub workflow private-endpoint-runner label

To ensure that the private-endpoint runner is selected for your workflow, please add the following line to your `.github/workflows/<your-github-workflow.yaml>`.

```
# .github/workflows/my-awesome-workflow.yaml

name: My awesome private endpoint workflow
...
jobs:
    my-cool-job:
        name: My Awesome Workflow Job
        runs-on: [self-hosted, self-hosted-linux-amd64-jammy-private-endpoint-medium]
```

For the list of flavors available, please refer to the [GitHub runner usage guide](https://docs.google.com/document/d/1dwokjUyb2av9VibwEMURLYtulRrRaXKt116gYZXaFYg/edit?tab=t.0).

[Full PR example](https://github.com/canonical/github-runner-operator/pull/163) including GitHub workflow.

## GitHub secrets

In order to pass sensitive credentials to access your internal endpoints (e.g. OpenStack project credentials), please use the GitHub secrets and pass them to your workflow.  
Refer to the official [GitHub secrets guide](https://docs.github.com/en/actions/security-for-github-actions/security-guides/using-secrets-in-github-actions).  
Please note that GitHub workflow may leak secrets and the workflow should be manually confirmed that no secrets are leaked.

## Repository rules

The private endpoint runners will disallow any external contributors from running the workflow. This means that any jobs submitted from a fork of an external contributor will be disallowed.

## Do’s and Don’ts

Resources for private-endpoint runners are very limited. Furthermore, since it allows access to Canonical’s internal infrastructure, the usage comes with a lot of risks.  
**Do** use the private-endpoint runners:

* To access Canonical infrastructure-only provided resources

**Do not** use the private-endpoint runners:

* To test ARM64 architectures (use ARM64 runners for that)  
* To run whatever can be run with regular self-hosted runners
