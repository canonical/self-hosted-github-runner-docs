Usage of self-hosted GitHub Actions runners
===========================================

This section provides an overview of how to use self-hosted GitHub Actions runners at Canonical.


**Note that GitHub provides runners for amd64 and arm64 architectures. For public repositories, these should be tried first before using self-hosted runners. We have an** `enterprise subscription that provides 500 free concurrent GitHub-hosted runners <https://docs.github.com/en/actions/administering-github-actions/usage-limits-billing-and-administration#usage-limits>`_
**for our public repositories. See more use-cases in** :doc:`When to Use Self-Hosted Runners </usage/usecases>`.


The GitHub self-hosted runners are available on all GitHub repositories within the Canonical organization: `https://github.com/canonical <https://github.com/canonical>`_.
Define a workflow file in the “.github/workflows/” folder in that repository. Here is a quickstart guide for GitHub actions: `https://docs.github.com/en/actions/quickstart <https://docs.github.com/en/actions/quickstart>`_.


.. toctree::
   :maxdepth: 2

    Available Runners </usage/available_runners>
    Example </usage/example>
    When to Use Self-Hosted Runners </usage/usecases>
    FAQ </usage/faq>