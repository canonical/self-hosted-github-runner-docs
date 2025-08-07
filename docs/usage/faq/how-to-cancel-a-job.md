# How can I cancel a job?

You can try through the web interface first.

If it doesn't work, you can force-cancel the job using the Github API through the Github CLI:

```
gh api -X POST /repos/canonical/wazuh-server-operator/actions/runs/$YOURWORKFLOWID/force-cancel
```
