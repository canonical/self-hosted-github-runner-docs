Usage of self-hosted GitHub Actions runners
===========================================

This section provides an overview of how to use self-hosted GitHub Actions runners at Canonical.

.. note::
  GitHub provides runners for amd64 and arm64 architectures. For public repositories, these should be tried first before using self-hosted runners. We have an `enterprise subscription that provides 500 free concurrent GitHub-hosted runners <https://docs.github.com/en/actions/reference/limits#job-concurrency-limits-for-github-hosted-runners>`_
  for our public repositories. See more use-cases in :ref:`when_to_use_runners`.

The GitHub self-hosted runners are available on all GitHub repositories within the Canonical organization: `https://github.com/canonical <https://github.com/canonical>`_.
Define a workflow file in the “.github/workflows/” folder in that repository. Here is a quickstart guide for GitHub actions: `https://docs.github.com/en/actions/quickstart <https://docs.github.com/en/actions/quickstart>`_.


.. toctree::
   :maxdepth: 1

    Available Runners </usage/available_runners>
    Example </usage/example>
    When to Use Self-Hosted Runners </usage/usecases>
    Private Endpoint Runners </usage/private_endpoint_runners>
    FIPS Runners </usage/fips_runners>
    SSH into the self-hosted runner with tmate </usage/tmate_ssh>
    FAQ </usage/faq/index>
