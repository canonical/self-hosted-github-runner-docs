(fips_runners)=

# FIPS runners

We provide special runners with [FIPS](https://en.wikipedia.org/wiki/Federal_Information_Processing_Standards)-compliant images. 
These images are created using an [Ubuntu Pro client with FIPS enabled](https://documentation.ubuntu.com/pro-client/en/v30/howtoguides/enable_fips/).
The client is then detached. This means that you cannot enable other Ubuntu Pro content on the machines,
unless you provide your own Ubuntu Pro token in the GitHub Actions run (e.g. via a GitHub secret).

Ubuntu Pro is gated content available to Canonical employees but which requires registration for external users. 
Therefore, publishing any artefact based on Ubuntu Pro content (e.g. as a GitHub artefact for external download) 
is not allowed. There may be exceptions to this rule (e.g. explicit permission from executive level). 
It is your responsibility to comply with this rule; Platform Engineering only provides the runners and does not control usage.

Similar to the private endpoint runners, the FIPS runners will disallow any external contributors
from running the workflow. This means that any jobs submitted from a fork of an external contributor will be disallowed.
