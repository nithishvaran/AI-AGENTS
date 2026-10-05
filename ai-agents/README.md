# Jenkins Failure Analysis Agent

On a failed build, the Jenkinsfile runs this LangChain analyzer. It retrieves the build console log, gathers commits since the previous successful build (or recent commits when that range is unavailable), asks Claude for an evidence-based diagnosis, and emails the report through Gmail SMTP.

## Jenkins setup

Add these credentials in Jenkins (Manage Jenkins > Credentials) with the exact IDs below:

| Credential ID | Type | Values |
| --- | --- | --- |
| `claude-api-key` | Secret text | Your Anthropic API key |
| `jenkins-api-token` | Username with password | Jenkins username and API token; grant read access to the job/build |
| `gmail-app-password` | Username with password | Gmail address and a Google app password (not your account password) |
| `failure-email-to` | Secret text | Destination email address |

The Jenkins agent needs `python3` with `venv`, Git, network access to Jenkins/Anthropic/Gmail, and the Credentials Binding plugin. The Jenkins API credentials let the analyzer retrieve `BUILD_URL/consoleText`.

The console log and commit diff are sent to Anthropic for analysis. Confirm that this is acceptable for your repository and build logs before enabling the job.

Optionally set the `CLAUDE_MODEL` environment variable on the Jenkins job to override the default `claude-sonnet-4-5` model.

The report runs only after a failed build. If Python dependency installation, Claude analysis, or email delivery fails, Jenkins will show that post-build error in the console log; the original build remains failed.
