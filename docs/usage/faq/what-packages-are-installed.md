(what-packages-are-installed)=

# What packages are installed?

The default Canonical hosted GitHub runners do not have matching parity with 
[GitHub hosted runners](https://github.com/actions/runner-images/blob/main/images/ubuntu)
in regards to the system setup.

## Installed packages

On top of the system packages that are installed by default on the base images ([focal](https://packages.ubuntu.com/focal/allpackages),
[jammy](https://packages.ubuntu.com/jammy/allpackages), [noble](https://packages.ubuntu.com/noble/allpackages)),
the following packages are installed from apt package repository.

- `build-essential`
- `cargo`
- `docker.io`
- `gh`
- `jq`
- `npm`
- `pkg-config`
- `python-is-python3`
- `python3-dev`
- `python3-pip`
- `rustc`
- `shellcheck`
- `socat`
- `tar`
- `time`
- `unzip`
- `wget`

The following packages are also installed from different sources.

- `yarn` (installed using NPM)
- `yq` (built from source)

## Installed packages for S390X, PPC64LE

On top of the installed packages above, the following packages are further installed on the runners
with S390X and PPC64LE architectures.

- `dotnet-runtime-8.0`
