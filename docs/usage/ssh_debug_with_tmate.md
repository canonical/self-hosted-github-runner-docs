# SSH into the self-hosted runner and debug with tmate

This guide covers how to setup [Canonical fork of tmate action workflow](https://github.com/canonical/action-tmate) with Canonical's organisation wide self-hosted runners.
The aim is to able to SSH into the self-hosted runner and perform manual debug within the self-hosted runner.

## How it works

The [tmate-ssh-server charm](https://charmhub.io/tmate-ssh-server) can be integrated with the [GitHub runner charm](https://charmhub.io/github-runner).
Once integrated, running the canonical/action-tmate workflow in the runner would setup everything needed for you to SSH into the runner.
Since the tmate-ssh-server charm in hosted on Canonical servers, you would need to connect to the Canonical VPN to access it.

## Steps

The process is split into the following steps:

1. Connect to the Canonical VPN
2. Setup GitHub SSH keys
3. Use the [canonical/action-tmate](https://github.com/canonical/action-tmate) workflow

## 1. Connect to the Canonical VPN

The Tmate SSH server is hosted on Canonical server. VPN is needed to access it to SSH into the runner.
For instructions on how to connect to Canonical VPN, please read [IS How to: Company Open VPN](https://wiki.canonical.com/InformationInfrastructure/IS/HowTo/CompanyOpenVPN).

## 2. Setup GitHub SSH keys

The workflow will setup SSH session to use the SSH keys registered on GitHub under your account.
To add SSH keys to your GitHub account, please see [Adding a new SSH key to your GitHub account](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)

## 3. Use the canonical/action-tmate workflow

The [canonical/action-tmate](https://github.com/canonical/action-tmate) workflow will setup everything needed for the SSH session.

Here is a sample workflow:

```yaml
name: Canonical action-tmate test
    
on: [pull_request]
    
jobs:
  build:
    runs-on: [self-hosted, linux, X64, jammy, large]
    steps:
    - name: Setup tmate session
      uses: canonical/action-tmate@main
```

The output of this workflow will include the SSH command to run to start the SSH session:

```console
Run canonical/action-tmate@main
Hit:1 http://security.ubuntu.com/ubuntu jammy-security InRelease
Hit:2 http://archive.ubuntu.com/ubuntu jammy InRelease
Hit:3 http://archive.ubuntu.com/ubuntu jammy-updates InRelease
Hit:4 http://archive.ubuntu.com/ubuntu jammy-backports InRelease
Reading package lists...
Reading package lists...
Building dependency tree...
Reading state information...
xz-utils is already the newest version (5.2.5-2ubuntu1).
xz-utils set to manually installed.
openssh-client is already the newest version (1:8.9p1-3ubuntu0.6).
openssh-client set to manually installed.
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
ssh -p10022 Nuw8ED2ymQsP6TcZ7LzXFDr8F@10.163.212.179

SSH: ssh -p 10022 Nuw8ED2ymQsP6TcZ7LzXFDr8F@10.163.212.179
or: ssh -i <path-to-private-SSH-key> -p10022 Nuw8ED2ymQsP6TcZ7LzXFDr8F@10.163.212.179
```

For this workflow run, the SSH command would be `ssh -p 10022 Nuw8ED2ymQsP6TcZ7LzXFDr8F@10.163.212.179`.
