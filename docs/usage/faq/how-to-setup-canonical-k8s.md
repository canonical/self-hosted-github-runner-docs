#

If you try to set up Canonical k8s directly you will run into

```
Bootstrap config verification failed: pre-init checks failed for node: The path '/run/containerd' required for the containerd socket already exists. This may mean that another service is already using that path, and it conflicts with the k8s snap. Please make sure that there is no other service installed that uses the same path, and remove the existing directory.(dev-only): You can change the default k8s containerd base path with the containerd-base-dir option in the bootstrap / join-cluster config file.
```

because docker is installed and already using containerd.

## Option A - removing docker and containerd

Uninstall docker.io and containerd

```
          sudo apt-get remove -y docker.io containerd
          sudo rm -rf /run/containerd
```


Before installing Canonical k8s, you should integrate Canonical k8s with our dockerhub mirror, otherwise bootstrapping
might fail due to rate limit issues



Set the mirror confing to point to  `MIRROR_CONFIG=/etc/containerd/hosts.d/docker.io` (the default of Canonical k8s).



```yaml
if [ -n "$DOCKERHUB_MIRROR" ]; then
sudo mkdir -p ${MIRROR_CONFIG}
sudo chown $USER ${MIRROR_CONFIG}
cat << EOF > ${MIRROR_CONFIG}/hosts.toml
[host."$DOCKERHUB_MIRROR"]
capabilities = ["pull", "resolve"]
EOF
fi
```


Finally, install and bootstrap Canonical k8s

```
          sudo snap install k8s --classic --channel=1.34-classic/stable
          sudo k8s bootstrap
          sudo k8s status --wait-ready
```


Total workflow :

```
name: CI
on: [push]
jobs:
  self-hosted:
    runs-on: ["self-hosted", "edge", "amd64"]
    steps:
      - name: Remove docker and containerd
        run: |
          export DEBIAN_FRONTEND=noninteractive
          sudo apt-get remove -y docker.io containerd
          sudo rm -rf /run/containerd
      - name: Setup integration with dockerhub mirror
        run: |
          MIRROR_CONFIG=/etc/containerd/hosts.d/docker.io
          if [ -n "$DOCKERHUB_MIRROR" ]; then
          sudo mkdir -p ${MIRROR_CONFIG}
          sudo chown $USER ${MIRROR_CONFIG}
          cat << EOF > ${MIRROR_CONFIG}/hosts.toml
          [host."$DOCKERHUB_MIRROR"]
          capabilities = ["pull", "resolve"]
          EOF
          fi
      - name: Install k8s snap and bootstrap
        run: |
          sudo snap install k8s --classic --channel=1.34-classic/stable
          sudo k8s bootstrap
          sudo k8s status --wait-ready
```

## Option B - Set different containerd-base-dir
With option B we keep docker and containerd, and specify a different base dir for Canonical-k8s.

First, choose a different basedir, e.g. `CONTAINERD_BASE_DIR=/opt/containerd`. 
Setup mirror-config accordingly : "MIRROR_CONFIG=${CONTAINERD_BASE_DIR}/k8s-containerd/etc/containerd/hosts.d/docker.io"

and then apply the dockerhub mirror config: 

```yaml
if [ -n "$DOCKERHUB_MIRROR" ]; then
sudo mkdir -p ${MIRROR_CONFIG}
sudo chown $USER ${MIRROR_CONFIG}
cat << EOF > ${MIRROR_CONFIG}/hosts.toml
[host."$DOCKERHUB_MIRROR"]
capabilities = ["pull", "resolve"]
EOF
fi
```

Then install and Canonical k8s and bootstrap using CONTAINERD_BASE_DIR

```
          sudo snap install k8s --classic --channel=1.34-classic/stable
          cat << EOF | sudo k8s bootstrap --file -
          containerd-base-dir: ${CONTAINERD_BASE_DIR}
          EOF 
          sudo k8s status --wait-ready
```


Total workflow:

```
name: CI
on: [push]
jobs:
  self-hosted:
    runs-on: ["self-hosted", "edge", "amd64"]
    steps:
      - name: Set different path for containerd
        run: |
          CONTAINERD_BASE_DIR=/opt/containerd >> $GITHUB_ENV

      - name: Setup integration with dockerhub mirror
        run: |
          MIRROR_CONFIG="${CONTAINERD_BASE_DIR}/k8s-containerd/etc/containerd/hosts.d/docker.io"
          if [ -n "$DOCKERHUB_MIRROR" ]; then
          sudo mkdir -p ${MIRROR_CONFIG}
          sudo chown $USER ${MIRROR_CONFIG}
          cat << EOF > ${MIRROR_CONFIG}/hosts.toml
          [host."$DOCKERHUB_MIRROR"]
          capabilities = ["pull", "resolve"]
          EOF
          fi

      - name: Install k8s snap and bootstrap
        run: |
          sudo snap install k8s --classic --channel=1.34-classic/stable
          cat << EOF | sudo k8s bootstrap --file -
          containerd-base-dir: ${CONTAINERD_BASE_DIR}
          EOF 
          sudo k8s status --wait-ready
```