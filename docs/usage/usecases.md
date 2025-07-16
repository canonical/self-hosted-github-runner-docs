# When to Use Self-Hosted Runners

We have an enterprise subscription that provides 500 free concurrent GitHub-hosted runners on our public repositories. You should try these first. If they don't work for your use case, using self-hosted runners is the way to go.

Typical use cases for self-hosted runners include:

\- You need more resources than GitHub Hosted provides.

\- You need to run jobs on private repositories (GitHub charges for these).

\- You need access to private endpoints using [private endpoint runners](#private-endpoint-runners).

\- You need runners on the S390X or PPC64EL architecture (experimental).