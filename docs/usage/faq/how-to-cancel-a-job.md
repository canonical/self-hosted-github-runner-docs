# How can I cancel a job?

You can try through the web interface first as described in [GitHub documentation](https://docs.github.com/en/actions/how-tos/manage-workflow-runs/cancel-a-workflow-run).

If it doesn't work, you can force-cancel the job using the Github API through the [Github CLI](https://github.com/cli/cli?tab=readme-ov-file#installation):

```
gh api -X POST /repos/canonical/wazuh-server-operator/actions/runs/$YOURWORKFLOWID/force-cancel
```

If you don't have GitHub CLI, you can also use `¢url`:

```
curl -X POST \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer $YOUR_TOKEN" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  "https://api.github.com/repos/OWNER/REPOSITORY/actions/runs/$YOURWORKFLOWID/force-cancel"
```
