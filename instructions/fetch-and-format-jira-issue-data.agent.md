# Fetch and Format Jira Issue Data

## Input Format

- Read `JIRA_BASE_URL`, `JIRA_EMAIL`, and `JIRA_API_TOKEN` from environment variables. Require an HTTPS Jira Cloud base URL; never accept credentials as command-line arguments or report content.
- Accept a Jira board ID as a non-empty string or integer.
- Accept the configured story-points field ID and request only the issue fields needed for the output: `summary`, `status`, `assignee`, `duedate`, and that configured field.
- Treat Jira's JSON issue records as the source data. Each record includes a `key` and a `fields` object; do not expect every requested field to be populated.

## Processing Steps

1. Validate the board ID, HTTPS base URL, credentials, and story-points field configuration before making requests.
2. Use the existing `JiraClient.list_board_issues()` API for board-scoped retrieval; do not duplicate authentication or HTTP handling.
3. Request only the required fields and collect all pages. Rely on the client's bounded retry handling for transient errors and rate limits.
4. Normalize each issue's key, summary, status name, assignee display name, due date, and configured story-points value. Build its direct URL as `{JIRA_BASE_URL.rstrip('/')}/browse/{issue_key}`.
5. Keep missing data explicit: render a null assignee as `Unassigned`, a null due date as `Not set`, and null story points as `Not estimated`. If a field was omitted or unavailable, render `Unavailable`; do not confuse this with a Jira value of zero or an explicitly empty field.
6. Sort formatted issues by issue key for deterministic output. If retrieval fails, report the error and do not present partial results as complete.

## Output Format

- Return one Markdown bullet per issue in this exact shape:
  + `- [ISSUE-KEY](JIRA-ISSUE-URL) Summary - Status: STATUS; Assignee: ASSIGNEE; Due: YYYY-MM-DD; Story points: VALUE`
- Preserve Jira's issue summary and status text; escape Markdown-sensitive characters when needed to keep the link and line structure valid.
- If retrieval succeeds and no issues match, return `- No matching issues.`
- Do not add an introduction, conclusion, or fields beyond the defined issue line unless requested.

## Constraints

- Never log, print, or include API credentials or tokens in generated output.
- Use HTTPS and the board's configured Jira scope. Do not broaden the query or fetch unrelated issue content.
- Do not include descriptions, comments, or other free-text fields by default.
- Do not invent, infer, or silently default missing values; in particular, never treat missing story points as zero.
- Do not label an empty result when Jira retrieval failed or returned an invalid response.
- Keep API failures concise and actionable without exposing secrets or unnecessary issue data.