# Send WhatsApp Message

GitHub Action that sends a WhatsApp message from a workflow: deploy notices, failed builds, release announcements to a group.

It sends from a regular WhatsApp number you link by QR code, through the [WhatsApp Messaging API by Retention Stack](https://whatsapp-messaging.retentionstack.agency). No WhatsApp Business account or Meta template approval. There is a free tier.

## Setup

1. Sign in at [whatsapp-messaging.retentionstack.agency/start](https://whatsapp-messaging.retentionstack.agency/start), link your number, and copy a session token (`ch_live_...`).
2. Add it as a repository secret named `WHATSAPP_API_KEY`.

## Usage

```yaml
- uses: jevil25/whatsapp-send-action@v1
  with:
    api-key: ${{ secrets.WHATSAPP_API_KEY }}
    to: '14155550123'
    message: 'Deployed ${{ github.repository }}@${{ github.sha }}'
```

Notify a group when a build fails:

```yaml
- uses: jevil25/whatsapp-send-action@v1
  if: failure()
  with:
    api-key: ${{ secrets.WHATSAPP_API_KEY }}
    to: '120363012345678901@g.us'
    message: '${{ github.workflow }} failed: ${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}'
```

## Inputs

| Name | Required | Description |
|---|---|---|
| `api-key` | yes | Session token (`ch_live_...`) or account key (`ak_live_...`). Use a secret. |
| `to` | yes | Phone number with country code, digits only, or a group ID ending in `@g.us`. |
| `message` | yes | Message text. |
| `session` | no | Session name. Leave as `default` with a session token; set it with an account key. |

## Outputs

| Name | Description |
|---|---|
| `response` | JSON response from the API. |

The step fails if the API returns a non-2xx status, and the error message from the API is shown in the log.

Runs on Linux, macOS and Windows runners (uses `bash`, `curl` and `jq`).

## Notes

This uses a linked device, like WhatsApp Web, not Meta's Cloud API. WhatsApp's rules on automated and bulk messaging still apply to the linked number.

## License

MIT
