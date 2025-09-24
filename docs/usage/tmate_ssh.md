(tmate_ssh)=

# SSH into the self-hosted runner with tmate

This guide covers how to setup [Canonical fork of tmate action workflow](https://github.com/canonical/action-tmate) with Canonical's organisation wide self-hosted runners.
The aim is to be able to SSH into the self-hosted runner and perform manual debug within the self-hosted runner.

## How it works

The [tmate-ssh-server charm](https://charmhub.io/tmate-ssh-server) can be integrated with the [GitHub runner charm](https://charmhub.io/github-runner).
Once integrated, running the `canonical/action-tmate` workflow in the runner would setup a connection to the tmate server from the self-hosted runner host. Under the hood, it uses the following environment variables to setup a secure SSH connection: `TMATE_SERVER_HOST`, `TMATE_SERVER_PORT`, `TMATE_SERVER_RSA_FINGERPRINT`, `TMATE_SERVER_ED25519_FINGERPRINT`.
Since the tmate-ssh-server charm is hosted on Canonical servers, you must connect to the Canonical VPN to access it.

## Steps

The process is split into the following steps:

1. Connect to the Canonical VPN
2. Set up GitHub SSH keys
3. Use the [`canonical/action-tmate`](https://github.com/canonical/action-tmate) workflow

## 1. Connect to the Canonical VPN

The tmate SSH server is hosted on Canonical server. VPN is needed to access it to SSH into the runner.
For instructions on how to connect to Canonical VPN, please read [Using our VPN](https://library.canonical.com/information-services-&-resources/using-our-vpn).

## 2. Set up GitHub SSH keys

The workflow will set up SSH session to use the SSH keys registered on GitHub under your account.
To add SSH keys to your GitHub account, please see [Adding a new SSH key to your GitHub account](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)

## 3. Use the `canonical/action-tmate` workflow

The [`canonical/action-tmate`](https://github.com/canonical/action-tmate) workflow will set up everything needed for the SSH session.

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

For this workflow, the SSH command would be `ssh -p 10022 Nuw8ED2ymQsP6TcZ7LzXFDr8F@10.163.212.179`.

## Optional: Use the `canonical/action-tmate` workflow

To use SSH into the runners without having to block the workflow, use `action-tmate` in detached
mode.

```yaml
name: Canonical action-tmate test
    
on: [pull_request]
    
jobs:
  build:
    runs-on: [self-hosted, linux, X64, jammy, large]
    steps:
    - name: Setup tmate session
      uses: canonical/action-tmate@main
      with:
        detached: true
```

## Further information

For further usage of the `action-tmate`, refer to the
[documentation](https://github.com/canonical/action-tmate).
