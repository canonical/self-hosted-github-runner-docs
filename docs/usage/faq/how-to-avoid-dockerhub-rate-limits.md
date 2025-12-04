# How can I avoid dockerhub rate limits?

We might run into [Docker Hub pull limits](https://docs.docker.com/docker-hub/download-rate-limit/) depending on the usage of the self-hosted runners. 
Platform Engineering operates an own DockerHub Mirror in PS6, to avoid the rate-limit issue.
We pre-configure Docker inside the runners to use the DockerHub cache.

Also, for further customisations on your side (e.g. for MicroK8s or Canonical K8s),
we print instructions in the "Setup runner step" for how to use the mirror, 
basically using the `DOCKERHUB_MIRROR` environment variable 

## MicroK8s

If you setup MicroK8s using the [charm actions](https://github.com/charmed-kubernetes/actions-operator), then the setup of the DockerHub mirror is  auto configured for you. 
Otherwise, if you install MicroK8s on your own,  you can check the MicroK8s instructions: [https://microk8s.io/docs/dockerhub-limits](https://microk8s.io/docs/dockerhub-limits).

## Canonical K8s
Canonical K8s ships with its own container runtime, which conflicts with the pre-installation of docker.
You have two options to configure the :

- uninstall docker and containerd:
-   sudo apt-get remove -y docker.io containerd

You can use the following code in your CI workflow after installing the `k8s` snap.

```yaml
if [ -n "$DOCKERHUB_MIRROR" ]; then
MIRROR_CONFIG=/etc/containerd/hosts.d/docker.io

sudo mkdir -p ${MIRROR_CONFIG}
sudo chown $USER ${MIRROR_CONFIG}
cat << EOF > ${MIRROR_CONFIG}/hosts.toml
[host."$DOCKERHUB_MIRROR"]
capabilities = ["pull", "resolve"]
EOF
fi
```

If you have overridden the containerd location (by using [containerd-base-dir](https://documentation.ubuntu.com/canonical-kubernetes/latest/snap/reference/config-files/bootstrap-config/#containerd-base-dir)), you will need to adapt the `MIRROR_CONFIG` value in the snippet accordingly.
