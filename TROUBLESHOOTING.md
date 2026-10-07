# My n8n alert didn't fire: checklist

Built from seven tests run on a real n8n 2.35.7 (self-hosted, regular mode): each test workflow failed on purpose, and a local test mail server recorded which ones produced an alert. Go top to bottom.

1. **Did the failing run come from a real trigger?** Clicking *Execute workflow* in the editor shows the red error on the node but does **not** run the error workflow (tested). Run it from its schedule or webhook (a production execution) instead.
2. **Did you select the error workflow in the failing workflow?** Open the failing workflow: Settings > Error workflow > pick this one. With nothing selected, nothing is sent (tested). Do this in every workflow you want covered.
3. **Is the error workflow published/active?** An unpublished error workflow is silently skipped (tested). On a self-hosted n8n the only trace is a server log line: `Calling Error Workflow for "<id>". Workflow "<id>" is not active and cannot be executed`. Search your logs for `is not active and cannot be executed`.
4. **Does the failing node have Continue On Fail (On Error: continue) set?** Then the execution doesn't fail, so no error workflow runs (tested). Change the setting or route the node's error output to your own alert.
5. **Is the alert workflow's email step set up?** Choose your SMTP credential on the Email alert node and set the from/to addresses. (If the error workflow runs but no mail arrives, check its own execution in the Executions list.)
6. **Did the workflow succeed but do nothing?** An error workflow only sees errors. A run that processes zero rows is a success. Add a check (IF on the row count or a field) that routes to a *Stop and Error* node when it fails; that turns it into a real error. This one is advice from the n8n forum, not something I tested.

## Good to know (tested)
- An **Error Trigger inside the same workflow** also worked in 2.35.7, with or without selecting the workflow as its own error workflow (one email, no duplicate). A shared error workflow like this one is still easier to maintain.
- To test without real mail, see `testing/` (local SMTP sink).

## Not tested
n8n Cloud, queue mode, other n8n versions, errors inside sub-workflows (Execute Workflow node), AI agent sub-node errors, timeouts and killed executions, and notification channels other than the Send Email node.

*Created with AI assistance (Claude) and tested as described here. n8n is a trademark of n8n GmbH; this repo is not affiliated with or endorsed by n8n.*
