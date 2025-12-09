(how_to_set_up_canonical_k8s)=

# How can I set up Canonical Kubernetes in my workflow?

## Option A - removing Docker and containerd (Recommended)

If Docker is not required in your workflow, uninstall `docker.io` and `containerd`

```bash
sudo apt-get remove -y docker.io containerd
sudo rm -rf /run/containerd
```


Before installing Canonical Kubernetes, you should integrate Canonical Kubernetes with our DockerHub mirror,
otherwise bootstrapping  might fail due to DockerHub rate limit issues (see also {ref}`how_to_avoid_dockerhub_rate_limits`).

Set the docker.io mirror config file to point to  `MIRROR_CONFIG=/etc/containerd/hosts.d/docker.io` (the default location Canonical Kubernetes).


```bash
if [ -n "$DOCKERHUB_MIRROR" ]; then
sudo mkdir -p ${MIRROR_CONFIG}
sudo chown $USER ${MIRROR_CONFIG}
cat << EOF > ${MIRROR_CONFIG}/hosts.toml
[host."$DOCKERHUB_MIRROR"]
capabilities = ["pull", "resolve"]
EOF
fi
```


Finally, install and bootstrap Canonical Kubernetes

```bash
sudo snap install k8s --classic --channel=1.34-classic/stable
sudo k8s bootstrap
sudo k8s status --wait-ready
```


Example workflow :

```yaml
name: CI
on: [push]
jobs:
  self-hosted:
    runs-on: ["self-hosted-linux-amd64-noble-medium"]
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

      - name: Your tests
        run: |
          ...
```

## Option B - Set different containerd-base-dir

Option B is useful if you need to still use Docker on your machine. With option B we keep Docker and containerd,
and specify a [different containerd base directory](https://documentation.ubuntu.com/canonical-kubernetes/latest/snap/reference/config-files/bootstrap-config/#containerd-base-dir) for Canonical Kubernetes.

First, choose a different base directory, e.g. `CONTAINERD_BASE_DIR=/opt/containerd`. 
Then set the mirror config to point to the expected config based on your base directory: `MIRROR_CONFIG="${CONTAINERD_BASE_DIR}/k8s-containerd/etc/containerd/hosts.d/docker.io"`

Afterwards, apply the DockerHub mirror config: 

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

Then install Canonical Kubernetes and bootstrap using the aforementioned  `CONTAINERD_BASE_DIR`

```bash
sudo snap install k8s --classic --channel=1.34-classic/stable
sudo k8s bootstrap --file - <<EOF
containerd-base-dir: ${CONTAINERD_BASE_DIR}
cluster-config:
network:
  enabled: true
dns:
  enabled: true
gateway:
  enabled: true
local-storage:
  enabled: true
EOF
sudo k8s status --wait-ready
```


Example workflow:

```yaml
name: CI
on: [push]
jobs:
  self-hosted:
    runs-on: ["self-hosted-linux-amd64-noble-medium"]
    steps:
      - name: Set different path for containerd
        run: |
          echo "CONTAINERD_BASE_DIR=/opt/containerd" >> $GITHUB_ENV

      - name: Setup integration with DockerHub mirror
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
          sudo k8s bootstrap --file - <<EOF
          containerd-base-dir: ${CONTAINERD_BASE_DIR}
          cluster-config:
            network:
              enabled: true
            dns:
              enabled: true
            gateway:
              enabled: true
            local-storage:
              enabled: true
          EOF
          sudo k8s status --wait-ready

      - name: Your tests
        run: |
          ...
```