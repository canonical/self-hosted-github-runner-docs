# How to use GitHub Self-Hosted Runners

This section describes how to use the GitHub self-hosted runners to run GitHub actions.

**Note that GitHub provides runners for amd64 and arm64 architectures. For public repositories, these should be tried first before using self-hosted runners. We have an [enterprise subscription that provides 500 free concurrent GitHub-hosted runners](https://docs.github.com/en/actions/administering-github-actions/usage-limits-billing-and-administration#usage-limits) for our public repositories. See more use-cases in [When to Use Self-Hosted Runners](#when-to-use-self-hosted-runners).**

The GitHub self-hosted runners are available on all GitHub repositories within the canonical organization: [https://github.com/canonical](https://github.com/canonical). Define a workflow file in the “.github/workflows/” folder in that repository. Here is a quickstart guide for GitHub actions: [https://docs.github.com/en/actions/quickstart](https://docs.github.com/en/actions/quickstart)

### Available runners

Currently,

 the following flavors have been deployed:

* 4 CPU and 16 GiB memory with 50 GiB disk labeled as “medium”  
* 8 CPU and 32 GiB of memory with 50 GiB of disk labeled as “large”  
* 16 CPU and 64 GiB of memory with 100 GiB of disk labeled “xlarge”  
* 32 CPU and 128 GiB of memory with 100 GiB of disk labeled “2xlarge”  
* 32 CPU and 128 GiB of memory with 500 GiB of disk labeled “2xlarge-extra”

The following architectures are supported:

* ARM64  
* AMD64 (GitHub uses the label X64)  
* S390x (**experimental, may be removed anytime**)  
* PPC64EL  (**experimental, may be removed anytime**)

The following base images are supported:

* Noble  
* Jammy  
* Focal

You can request runners by specifying architecture, base-image and flavor labels in your workflow, e.g. 

\[self-hosted, linux, amd64, large, jammy\]

would give you a large runner with base jammy on amd64. Every runner also has a single label to identify, with the pattern *self-hosted-linux-\<arch\>-\<base\>-\<flavor\>*, so e.g. *self-hosted-linux-amd64-jammy-large* for the large runner mentioned above.

**We recommend using the single label, which provides precisely the runner you need.** **Otherwise, please ensure that you specify the runner as precisely as possible by specifying all labels, so e.g. \[x64, large, jammy\]. This is because we may spawn additional sizes, Ubuntu OS versions and architectures in the future and without being precise GitHub will choose the allocation of the runner to your job.** 

The following is a list of runners currently available:

| ARCH | BASE-IMAGE | FLAVOR | SINGLE-LABEL | ADDITIONAL\_LABELS |
| :---- | :---- | :---- | :---- | :---- |
| amd64 | focal | large | self-hosted-linux-amd64-focal-large |  |
| amd64 | jammy | large | self-hosted-linux-amd64-jammy-large |  |
| amd64 | jammy | large | self-hosted-linux-amd64-jammy-large-tiobe | large-tiobe |
| amd64 | focal | medium | self-hosted-linux-amd64-focal-medium |  |
| amd64 | jammy | medium | self-hosted-linux-amd64-jammy-private-endpoint-medium |  |
| amd64 | jammy | medium | self-hosted-linux-amd64-jammy-medium |  |
| amd64 | jammy | medium | self-hosted-linux-amd64-jammy-edge | edge |
| amd64 | jammy | medium | self-hosted-linux-amd64-jammy-tiobe | tiobe |
| amd64 | jammy | xlarge | self-hosted-linux-amd64-jammy-xlarge |  |
| amd64 | noble | 2xlarge | self-hosted-linux-amd64-noble-2xlarge |  |
| amd64 | noble | 2xlarge | self-hosted-linux-amd64-noble-2xlarge-tiobe | 2xlarge-tiobe |
| amd64 | noble | 2xlarge-extra | self-hosted-linux-amd64-noble-2xlarge-extra |  |
| amd64 | noble | large | self-hosted-linux-amd64-noble-large |  |
| amd64 | noble | large | self-hosted-linux-amd64-noble-large-tiobe | large-tiobe |
| amd64 | noble | medium | self-hosted-linux-amd64-noble-medium |  |
| amd64 | noble | medium | self-hosted-linux-amd64-noble-tiobe | tiobe |
| amd64 | noble | xlarge | self-hosted-linux-amd64-noble-xlarge |  |
| arm64 | jammy | large | self-hosted-linux-arm64-jammy-large |  |
| arm64 | jammy | medium | self-hosted-linux-arm64-jammy-medium |  |
| arm64 | noble | medium | self-hosted-linux-arm64-noble-edge | edge |
| arm64 | jammy | xlarge | self-hosted-linux-arm64-jammy-xlarge |  |
| arm64 | noble | large | self-hosted-linux-arm64-noble-large |  |
| arm64 | noble | medium | self-hosted-linux-arm64-noble-medium |  |
| arm64 | noble | xlarge | self-hosted-linux-amd64-noble-xlarge |  |
| ppc64el | jammy | medium | self-hosted-linux-ppc64el-noble-edge |  |
| s390x | jammy | medium | self-hosted-linux-s390x-noble-edge |  |

Note that this list includes private endpoint runners and TIOBE runners (you see the additional label). Please refer to [Private endpoint runners](#private-endpoint-runners) and [TIOBE Runners](#tiobe-runners) in order to understand when to use those.

### Example

These runners can be used in the same way as the GitHub provided runners. The following is an example workflow file which will make use of all the kinds of runners available:

```

name: Tests

on:
 push:

jobs:
 test-self-hosted-large:
   runs-on: [self-hosted-linux-amd64-jammy-large]
   steps:
     - uses: actions/checkout@v3
     - run: curl https://www.google.com.au
 test-self-hosted-xlarge:
   runs-on: [self-hosted, linux, X64, jammy, xlarge]
   steps:
     - uses: actions/checkout@v3
     - run: echo hi
```

### When to Use Self-Hosted Runners

We have an enterprise subscription that provides 500 free concurrent GitHub-hosted runners on our public repositories. You should try these first. If they don't work for your use case, using self-hosted runners is the way to go.

Typical use cases for self-hosted runners include:

\- You need more resources than GitHub Hosted provides.

\- You need to run jobs on private repositories (GitHub charges for these).

\- You need access to private endpoints using [private endpoint runners](#private-endpoint-runners).

\- You need runners on the S390X or PPC64EL architecture (experimental).

### Private endpoint runners

Please refer to the [private-endpoint runners usage guide](https://docs.google.com/document/d/16tAeCLcFEy9UeR3GzS32k8P4ttrsFA5Apg3nVdFLN9w/edit?tab=t.0#heading=h.pgq3j0bim79s).

### TIOBE Runners

These are runners that come with pre-installed software to perform the TIOBE scan faster. Please refer to [SEC0024- SSDLC - Static Code Analysis](https://docs.google.com/document/d/1PRi8F-8IAPCtESDB_qN9HGyvq5t4tHFdJkG8Bu7WRak/edit?tab=t.0#heading=h.uww97wqcz7zi).

### FAQ

Please note:

* All HTTP(S) network access outside of Canonical's infrastructure will be passed through one of the Squid proxies (there is one in each of PS5, PS6, and PS7). This is done transparently using [aproxy](https://github.com/canonical/aproxy). In the past, rules have been added to the firewall to allow traffic for other protocols, such as Git or SSH. This is no longer possible with our newest cloud PS7, where most of our runners are spawned. All protocols must pass through the proxy. For TCP traffic, you can use the [PROXY CONNECT](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods/CONNECT) method to tunnel the transport and ask for the relevant [ACLs in the Squid proxy](https://git.launchpad.net/canonical-is-internal-proxy-configs/tree/ps7.conf). Note that the proxy host and port are logged in the pre-job section of your job.  
* To troubleshoot issues with your workflow you can [SSH into the runners](https://discourse.canonical.com/t/use-tmate-debug-ssh-with-self-hosted-runners/3289)  
* The default GitHub runners come with certain software pre-installed which is different to the software that is pre-installed on the self-hosted runners. If there is something missing, please ask us to evaluate including it.  
* The Canonical network is not reachable from the regular self-hosted runners, Please use the [private endpoint runners](https://discourse.canonical.com/t/private-endpoint-runners-user-guide/4853) for this purpose.  
* Note that the default limit for a job (a workflow may be composed into multiple jobs using multiple runners) is usually 6 hours, but this limit can be increased using the [timeout-minutes](https://docs.github.com/en/actions/writing-workflows/workflow-syntax-for-github-actions#jobsjob_idtimeout-minutes) key up to 5 days.  
* We might run into [Docker Hub pull limits](https://docs.docker.com/docker-hub/download-rate-limit/) depending on the usage of the self-hosted runners. We pre-configure Docker inside the runners to use the DockerHub cache and, if you setup microk8s using the charm actions, then it is also auto configured for you. Otherwise, we print instructions in the Setup runner step for how to use the mirror, basically using the DOCKERHUB\_MIRROR environment variable and you can check the microk8s instructions: [https://microk8s.io/docs/dockerhub-limits](https://microk8s.io/docs/dockerhub-limits)   
* If your job uses Juju & MicroK8s version 1.26 or earlier you will need to have [a workflow like this](https://github.com/canonical/bundle-kubeflow/blob/a2b88e8cdd5ba1f84e85921087c4b812abc91424/.github/workflows/full-bundle-tests.yaml), because MicroK8s by default configures itself to use Google’s DNS servers, and access to those isn’t allowed from where these runners are hosted.  
  * Specifically we’re getting the local DNS server for the instance, and then configuring MicroK8s to use that rather than its default.
