# Example

The self-hosted runners can be used in the same way as the GitHub provided runners.

Define a workflow file in the `.github/workflows/` folder in your repository (needs to be under the Canonical organisation):

```

name: Tests

on:
 push:

jobs:
 test-self-hosted-large:
   runs-on: [self-hosted-linux-amd64-jammy-large]
   steps:
     - uses: actions/checkout@v3
     - run: curl https://www.google.com.au
 test-self-hosted-xlarge:
   runs-on: [self-hosted, linux, X64, noble, xlarge]
   steps:
     - uses: actions/checkout@v3
     - run: echo hi
```

This workflow would trigger two jobs, where the first job would run on a large runner using jammy as OS, and the second one using an xlarge runner on noble.
See {ref}`available_runners` for the available runners.
