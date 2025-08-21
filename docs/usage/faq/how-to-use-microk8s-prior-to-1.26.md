# How can I use MicroK8s prior to 1.26?

If your job uses Juju and MicroK8s version 1.26 or earlier you will need to have [a workflow like this](https://github.com/canonical/bundle-kubeflow/blob/a2b88e8cdd5ba1f84e85921087c4b812abc91424/.github/workflows/full-bundle-tests.yaml), because MicroK8s by default configures itself to use Google’s DNS servers, and access to those servers isn’t allowed from where the runners are hosted.  

  * Specifically we’re getting the local DNS server for the instance, and then configuring MicroK8s to use that rather than its default.
