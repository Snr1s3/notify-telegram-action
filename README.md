# Notify Me Through Telegram

This GitHub Action sends a Telegram notification whenever specified workflows finish running in your repository. The notification includes the repository name, commit message, author, workflow name, and the workflow status (success or failure), helping you monitor your CI/CD results in real time.

## Features

- Notifies you on Telegram when a workflow completes
- Shows repository name, commit message, author, workflow name, and status
- Uses Markdown formatting for clear, readable messages

## Usage

1. **Add the Action to your workflow:**

   Copy the contents of `action.yml` into your workflow file (e.g., `.github/workflows/notify-me.yml`).

2. **Configure your workflow:**

   - Replace `"Example Name"` in `workflows: ["Example Name"]` with the names of the workflows you want to monitor (e.g., `"Go Test"`).
   - Make sure your workflow includes the `workflow_run` trigger.

3. **Set up secrets:**

   - Go to your repository settings → Secrets and variables → Actions.
   - Add these secrets:
     - `TELEGRAM_BOT_TOKEN`: Your Telegram bot token from BotFather.
     - `TELEGRAM_CHAT_ID`: The chat ID where notifications should be sent.

## Example Workflow

```yaml
on:
  workflow_run:
    workflows: ["Go Test"]
    types:
      - completed
jobs:
  notify:
    runs-on: ubuntu-latest
    steps:
      - name: Send Telegram notification
        run: |
          TITLE="🔔 *${{ github.repository }}*"
          COMMIT_MSG="${{ github.event.workflow_run.head_commit.message }}"
          AUTHOR="${{ github.event.workflow_run.head_commit.author.name }}"
          COMMIT_URL="${{ github.server_url }}/${{ github.repository }}/commit/${{ github.event.workflow_run.head_commit.id }}"
          WORKFLOW="${{ github.event.workflow_run.name }}"
          STATUS=""
          if [ "${{ github.event.workflow_run.conclusion }}" = "success" ]; then
            STATUS="✅ *Workflow:* ${WORKFLOW} _succeeded_"
          else
            STATUS="❎ *Workflow:* ${WORKFLOW} _failed_"
          fi
          MSG="${TITLE} *Commit:* [${COMMIT_MSG}](${COMMIT_URL}) *Author:* ${AUTHOR} ${STATUS}"
          curl -s -X POST "https://api.telegram.org/bot${{ secrets.TELEGRAM_BOT_TOKEN }}/sendMessage" \
            -d chat_id=${{ secrets.TELEGRAM_CHAT_ID }} \
            -d parse_mode=Markdown \
            -d text="${MSG}"
```

## Notes

- Make sure your Telegram bot is started and can send messages to your chat.
- You can monitor multiple workflows by listing their names in the `workflows` array.

---

Feel free to customize this README for your repository!