(operations)=

# Operations

This section provides an overview of how the runners are operated.

## Who

The self-hosted runners are currently operated by the [Platform Engineering Team](https://library.canonical.com/our-organisation/information-systems/platform-engineering). We don't have 
special permissions to access the underlying infrastructure, which means that we need to reach out to IS
in cases of infrastructure issues.

## How

We are operating using [juju](https://juju.is/) and deploying charms on the machine and k8s substrate. We are highly 
dependent on an operational underlying k8s cluster and OpenStack cloud.
We have basic alerts for the most common issues we are anticipating.

## Coverage

The service is operated in a best effort way. There is no SLA defined and the service is 
explicitly not covered 

- on weekends
- during Americas timezone

## How to get help

Please refer to {ref}`how_to_get_help`.