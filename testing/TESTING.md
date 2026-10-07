# Testing an n8n email workflow without sending real email

This is how the error-alert workflow in this repo was tested.

1. Run n8n locally (`npx n8n`) and a local SMTP sink: `pip install aiosmtpd && python smtp_sink.py mail.jsonl`.
2. In n8n create an SMTP credential: host `127.0.0.1`, port `2525`, TLS off.
3. Import `../error-alert.json`, select that credential, publish it.
4. Make a second workflow that always fails (a Code node with `throw new Error('demo')`) and set its Settings > Error workflow to the alert workflow. Trigger it from a Webhook node (the error workflow only runs for production executions, not manual ones).
5. Run it, then read `mail.jsonl`: assert that exactly one email arrived with the workflow name, node and error. Counting lines in `mail.jsonl` is the "assert send count" check.

The same sink works for any workflow that uses the Send Email node. For Google Sheets, test copies can swap the Sheets node for a Code node returning fixed rows. Tested with n8n 2.35.
