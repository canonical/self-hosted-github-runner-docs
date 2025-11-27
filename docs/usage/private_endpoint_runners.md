(private_endpoint_runners)=

# Private endpoint runners user guide

This guide covers how to start using the private-endpoint GitHub self-hosted runners which allow access to Canonical internal resources. For example, the private endpoint runners can allow connection to OpenStack or Juju controllers on PS5/PS6/PS7 for testing on different architectures only available within the Canonical infrastructure.

The private-endpoint runners are being migrated from PS6 to PS7. Currently, there is one deployment with jammy base on PS6. All other deployments are and will be on PS7 (upwards).

The following will be required to start using the private-endpoint runners. Each section is covered in detail below.

- PS7 private-endpoint runners
  - Network topology
  - Security implications
  - Setup to access resource on PS7
  - Setup to access resource on PS5/PS6
- PS6 private-endpoint runners
- Workflow private-endpoint-runner labels
- GitHub secrets
- Repository rules
- Do’s and Don’ts

## PS7 private-endpoint runners

Currently, the noble private-endpoint runners are hosted on PS7, such as `self-hosted-linux-amd64-noble-private-endpoint-medium`.

For the list of flavors available, please refer to the {ref}`available_runners`.

### Network topology

The private-endpoint self-hosted runners are virtual machines on PS7 and they are able to access resources on PS7 and PS5/PS6.
How it works will differ based on which ProdStack it is accessing.

#### PS7

Since the runners are on PS7, accessing resources will be relatively simple as the network traffic will be between OpenStack projects on the same OpenStack cluster.

OpenStack networking features can be utilized, such as setting up security group rules to allow and deny types of traffic. See [OpenStack networking](https://docs.openstack.org/mitaka/networking-guide/intro-os-networking.html) for more details.

#### PS5/PS6

Outgoing network traffic from PS7 virtual machine will need to go through the PS7 egress proxy.

For the runners on PS7 to reach resources on PS5/PS6, the network traffic will originate from the PS7 virtual machine hosting the runner, then the traffic would reach PS7 egress proxy and be forwarded to PS5/PS6 ingress.

### Security implications

For both resource on PS7 and PS5/PS6, enabling the access from the private-endpoint runner means anyone that can access the private-endpoint runner would be able to access the resource on PS5/PS6/PS7.
For this reason, the resource on PS5/PS6/PS7 should be guarded with authentication if the resource is not intended for company wide use.

For resources on PS5/PS6, the network traffic will pass through the PS7 egress proxy. Hence from the resource on PS5/PS6 the traffic would appear to come from the PS7 egress proxy.
As such for the resource on PS5/PS6 all packets would be from the IP of the PS7 egress proxy and the resource would not be able to distinguish where the packets came from.
This means restriction in network access to the resource on PS5/PS6 needs to be done on the PS7 egress proxy.
If the PS7 egress proxy rules are not set up correctly, it is possible for anyone with access to the PS7 egress proxy to send traffic to the resource on PS5/PS6.

### Setup to access resource on PS7

To allow traffic between two PS7 models, there is an automated IS request for it. The steps are as follows:

1. Go to [IS request](https://portal.admin.canonical.com/requests/new).
2. Select `Establish inter-model network peering (PS7)` in the dropdown menu.
3. Fill in the form. For the model, fill in `prod-ps7-github-runner-tenant-amd64-private-endpoint` as this is the model the private-endpoint runners are hosted in.
4. Get the approvals needed.

Once approved, the request should be automatically processed, and the private-endpoint runners should be able to access the resource on the other model.

If you have encountered issues, please check if the OpenStack security group is configured to allow the type of traffic you are using.
If you are using Juju, Juju should automatically manage the security group. You should be able to expose a resource through Juju.

### Setup to access resource on PS5/PS6

All network traffic going out of PS7 needs to go through the PS7 egress proxy, therefore the setup is split into two steps.

1. Allow traffic to pass through the PS7 egress proxy on the proxy side.
2. Allow traffic from the PS7 egress proxy to reach resources on the PS5/PS6 firewall side.

#### PS7 egress proxy

The PS7 egress proxy is a squid proxy with the configuration defined in the file `ps7.conf` within the git repo `canonical-is-internal-proxy-configs` on Launchpad.

First, the resource on PS5/PS6 needs to be defined in `ps7.conf`.
Here are examples for domain based and IP based definitions:

```
acl certification_canonical_com dstdomain certification.canonical.com
...
acl ps6_github_runner_dockerhub_cache_ip dst 162.213.35.28
```

The access list definitions with `acl` are generally at the top of the file.

Then allow `ps7_private_endpoint_runners` to access the defined access list, such as:

```
http_access allow ps7_private_endpoint_runners certification_canonical_com
...
http_access allow ps7_github_runners ps6_github_runner_dockerhub_cache_ip
```

These `http_access` rules must be placed before the following line in the file:

```
http_access deny ps7_github_runners to_rfc1918
```

#### Firewall for PS5/PS6 ingress

Once the PS7 egress proxy is redirecting the traffic, the company firewall needs to be configured to allow traffic from the egress proxy to the resource on PS5/PS6.

The company firewall is defined in the git repo `canonical-is-firewalls` on Launchpad.

The PS7 egress proxy source ips are incorporated  in the service definition of `services/is/github-runner/production-private-endpoint-runners` in the `canonical-is-firewalls` repository.

Therefore a rule needs to be defined from `services/is/github-runner/production-private-endpoint-runner` to the resource on the correct port.

Here are some example rules:

```yaml
    - comment: "Allow tmate ssh from ps7 prod runners"
      from: [services/is/github-runner/production-private-endpoint-runners]
      to: [services/is-charms/tmate-ssh-server/tmate-ssh-server-ps6]
      ports: [tcp/10022] # Custom port after 22 exposed by tmate-ssh-server charm
```

```yaml
    - comment: "Allow traffic from ps7 egress-proxies to access testflinger"
      from: [services/is/ps7/egress-proxies]
      to: [services/pes/certification-kubernetes-production/workers-public]
      ports: [http, https]
```

## PS6 private-endpoint runners

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
  from: [services/<team>/<team>-github-runner/<model name>]
  to: [services/is/juju/controllers-ps6]
  ports: [jujud-api]
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

For the list of flavors available, please refer to the {ref}`available_runners`.

[Full PR example](https://github.com/canonical/github-runner-operator/pull/163) including GitHub workflow.

## GitHub secrets

In order to pass sensitive credentials to access your internal endpoints (e.g. OpenStack project credentials), please use the GitHub secrets and pass them to your workflow.  
Refer to the official [GitHub secrets guide](https://docs.github.com/en/actions/security-for-github-actions/security-guides/using-secrets-in-github-actions).  
Please note that GitHub workflow may leak secrets and the workflow should be manually confirmed that no secrets are leaked.

## Repository rules

The private endpoint runners will disallow any external contributors from running the workflow. This means that any jobs submitted from a fork of an external contributor will be disallowed.

## Do’s and don’ts

Resources for private-endpoint runners are very limited. Furthermore, since it allows access to Canonical’s internal infrastructure, the usage comes with a lot of risks.  
**Do** use the private-endpoint runners:

- To access Canonical infrastructure-only provided resources

**Do not** use the private-endpoint runners:

- To test ARM64 architectures (use ARM64 runners for that)
- To run whatever can be run with regular self-hosted runners
