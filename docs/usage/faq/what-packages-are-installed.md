(what-packages-are-installed)=

# What packages are installed

The Canonical hosted GitHub runners do not have parity with 
[GitHub hosted runners](https://github.com/actions/runner-images/blob/main/images/ubuntu/Ubuntu2404-Readme.md)
in regards to the system setup.

## Installed packages

On top of the system packages that are installed by default on the base images (see manifest files
in each [release](https://releases.ubuntu.com/)), the following packages are installed from apt
package repository.

- build-essential
- cargo
- docker.io
- gh
- jq
- npm
- pkg-config
- python-is-python3
- python3-dev
- python3-pip
- rustc
- shellcheck
- socat # socat is used for proxying between the runner and the tmate-ssh-server
- tar
- time
- unzip
- wget

## S390X, PPC64LE installed packages

On top of the installed packages above, the following packages are further installed on the runners
with S390X and PPC64LE architectures.

- dotnet-runtime-8.0 # required to run GitHub runner binary
