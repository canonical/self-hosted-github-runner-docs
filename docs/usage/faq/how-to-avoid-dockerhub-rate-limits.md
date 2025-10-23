# How can I avoid dockerhub rate limits?

We might run into [Docker Hub pull limits](https://docs.docker.com/docker-hub/download-rate-limit/) depending on the usage of the self-hosted runners. We pre-configure Docker inside the runners to use the DockerHub cache and, if you setup MicroK8s using the charm actions, then it is also auto configured for you. Otherwise, we print instructions in the Setup runner step for how to use the mirror, basically using the `DOCKERHUB_MIRROR` environment variable and you can check the MicroK8s instructions: [https://microk8s.io/docs/dockerhub-limits](https://microk8s.io/docs/dockerhub-limits).

For Canonical Kubernetes users, you can use the following code in your CI workflow after installing the `k8s` snap.

```yaml
if [ -n "$DOCKERHUB_MIRROR" ]; then
MIRROR_CONFIG=/opt/containerd/k8s-containerd/etc/containerd/hosts.d/docker.io

sudo mkdir -p ${MIRROR_CONFIG}
sudo chown $USER ${MIRROR_CONFIG}
cat << EOF > ${MIRROR_CONFIG}/hosts.toml
[host."$DOCKERHUB_MIRROR"]
capabilities = ["pull", "resolve"]
EOF
fi
```
