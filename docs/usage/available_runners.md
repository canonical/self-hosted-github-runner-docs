(available_runners)=

# Available runners

Currently, the following flavors have been deployed:

- 4 CPU and 16 GiB memory with 50 GiB disk labeled as `medium`
- 8 CPU and 32 GiB of memory with 50 GiB of disk labeled as `large`
- 16 CPU and 64 GiB of memory with 100 GiB of disk labeled `xlarge`
- 32 CPU and 128 GiB of memory with 100 GiB of disk labeled `2xlarge`
- 32 CPU and 128 GiB of memory with 500 GiB of disk labeled `2xlarge-extra`

The following architectures are supported:

- `ARM64`
- `AMD64` (GitHub uses the label X64)
- `S390x` (**experimental, may be removed anytime**)
- `PPC64EL` (**experimental, may be removed anytime**)

The following base images are supported:

- Noble
- Jammy
- Focal

You can request runners by specifying architecture, base-image and flavor labels in your workflow, e.g.

\[self-hosted, linux, amd64, large, jammy\]

would give you a large runner with base jammy on amd64. Every runner also has a single label to identify, with the pattern _self-hosted-linux-\<arch\>-\<base\>-\<flavor\>_, so e.g. _self-hosted-linux-amd64-jammy-large_ for the large runner mentioned above.

**We recommend using the single label, which provides precisely the runner you need.** **Otherwise, please ensure that you specify the runner as precisely as possible by specifying all labels, so e.g. \[x64, large, jammy\]. This is because we may spawn additional sizes, Ubuntu OS versions and architectures in the future and without being precise GitHub will choose the allocation of the runner to your job.**

The following is a list of runners currently available:

| ARCH | BASE-IMAGE | FLAVOR | SINGLE-LABEL | ADDITIONAL_LABELS |
|---|------------|---|---|-------------------|
| amd64 | focal      | large | self-hosted-linux-amd64-focal-large |                   |
| amd64 | focal      | medium | self-hosted-linux-amd64-focal-medium |                   |
| amd64 | jammy      | large | self-hosted-linux-amd64-jammy-large |                   |
| amd64 | jammy      | large | self-hosted-linux-amd64-jammy-large-tiobe | large-tiobe       |
| amd64 | jammy      | medium | self-hosted-linux-amd64-jammy-private-endpoint-medium |                   |
| amd64 | jammy      | medium | self-hosted-linux-amd64-jammy-medium |                   |
| amd64 | jammy      | medium | self-hosted-linux-amd64-jammy-tiobe | tiobe             |
| amd64 | jammy      | xlarge | self-hosted-linux-amd64-jammy-xlarge |                   |
| amd64 | noble      | 2xlarge | self-hosted-linux-amd64-noble-2xlarge |                   |
| amd64 | noble      | 2xlarge | self-hosted-linux-amd64-noble-2xlarge-tiobe | 2xlarge-tiobe     |
| amd64 | noble      | 2xlarge-extra | self-hosted-linux-amd64-noble-2xlarge-extra |                   |
| amd64 | noble      | large | self-hosted-linux-amd64-noble-large |                   |
| amd64 | noble      | large | self-hosted-linux-amd64-noble-large-tiobe | large-tiobe       |
| amd64 | noble      | medium | self-hosted-linux-amd64-noble-edge | edge              |
| amd64 | noble      | medium | self-hosted-linux-amd64-noble-medium |                   |
| amd64 | noble      | medium | self-hosted-linux-amd64-noble-pfe-ci | pfe-ci            |
| amd64 | noble      | medium | self-hosted-linux-amd64-noble-private-endpoint-medium |                   |
| amd64 | noble      | medium | self-hosted-linux-amd64-noble-tiobe | tiobe             |
| amd64 | noble      | xlarge | self-hosted-linux-amd64-noble-xlarge |                   |
| amd64 | noble      | xlarge | self-hosted-linux-amd64-noble-private-endpoint-xlarge |                   |
| arm64 | jammy      | large | self-hosted-linux-arm64-jammy-large |                   |
| arm64 | jammy      | medium | self-hosted-linux-arm64-jammy-medium |                   |
| arm64 | noble      | medium | self-hosted-linux-arm64-noble-edge | edge              |
| arm64 | jammy      | xlarge | self-hosted-linux-arm64-jammy-xlarge |                   |
| arm64 | noble      | large | self-hosted-linux-arm64-noble-large |                   |
| arm64 | noble      | medium | self-hosted-linux-arm64-noble-medium |                   |
| arm64 | noble      | medium | self-hosted-linux-arm64-noble-medium-fips | fips              |
| arm64 | noble      | xlarge | self-hosted-linux-arm64-noble-xlarge |                   |
| ppc64el | noble      | medium | self-hosted-linux-ppc64el-noble-edge | edge              |
| s390x | noble      | medium | self-hosted-linux-s390x-noble-edge | edge              |

Note that this list includes special runners (you see the additional label). Please refer to the sections below in order to understand when to use those.

## Private endpoint runners

Please refer to the {ref}`private_endpoint_runners`.

## TIOBE runners

These are runners that come with pre-installed software to perform the TIOBE scan faster. Please refer to [SEC0024- SSDLC - Static Code Analysis](https://library.canonical.com/corporate-policies/information-security-policies/ssdlc/ssdlc---static-code-analysis#execute-tics-11).

## FIPS runners

These are runners coming with a FIPS-compliant image using Ubuntu Pro. Please refer to {ref}`fips_runners`.
