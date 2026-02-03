# Job: job-re-run

## Purpose
Re-executes a specific Rundeck job using an Execution ID and logs results to a PagerDuty incident. This job is designed for automated incident management workflows, providing transparency in job execution by capturing logs and updating incident status based on execution outcomes.

## Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `execution-id` | text | Yes | The unique identifier of the Rundeck execution to be re-run |
| `pd_incident_id` | text | Yes | PagerDuty incident ID where execution results will be logged |
| `pd_user_email` | text | Yes | PagerDuty user email for API authentication |
| `rba-token` | secure | Yes (hidden) | Rundeck API authentication token stored in Key Storage |
| `rba-url` | text | Yes | The base URL of the Rundeck instance (default: https://<runbook-automation-url>) |
| `pd-api-token` | secure | Yes (hidden) | PagerDuty API token stored in Key Storage |

## Workflow

1. **job-re-run**: Triggers the re-execution of the specified job using the execution ID
   - Extracts execution ID, job UUID, job name, and project from the response
   - Uses JSON mapper log filters to capture metadata

2. **wait-for-execution**: Monitors the re-run execution until completion
   - Polls execution status
   - If execution fails, captures failure status via error handler
   - Continues workflow regardless of execution outcome

3. **get-logs-from-rerun-execution**: Retrieves full execution logs via HTTP API call
   - Uses Rundeck API endpoint to fetch output in text format
   - Authenticates using the stored RBA token

4. **add-incident-note**: Posts execution results to the PagerDuty incident
   - Includes job name, re-run status, and link to execution logs
   - Uses PagerDuty note step plugin

5. **resolve-incident**: Resolves the PagerDuty incident if execution succeeded
   - Only runs if execution status is not "failed" (conditional rule)
   - Updates incident status to resolved with success message

## Expected Output

- **Success**:
  - Execution logs displayed in job output
  - PagerDuty incident updated with execution details and link
  - Incident automatically resolved

- **Failure**:
  - Execution logs displayed in job output
  - PagerDuty incident updated with failure status
  - Incident remains open for manual review

## Requirements

### Plugins
- `run-executions` - Native Rundeck plugin for triggering executions
- `result-executions` - Native Rundeck plugin for monitoring execution status
- `json-mapper` - Log filter for extracting JSON data
- `edu.ohio.ais.rundeck.HttpWorkflowStepPlugin` - HTTP Workflow Step Plugin
- `pd-note-step` - PagerDuty Note Step Plugin
- `pd-update-incident-step` - PagerDuty Update Incident Step Plugin
- `pagerduty-incident-output-capture` - PagerDuty Incident Output Capture Log Filter

### Key Storage
- `keys/rba-api-token` - Rundeck API token with execution permissions
- `keys/pagerduty/api-token` - PagerDuty API token with incident write permissions

### Permissions
- User must have permissions to re-run jobs in the target project
- PagerDuty API token must have access to modify the specified incident

### Network Access
- Outbound HTTPS access to Rundeck instance API
- Outbound HTTPS access to PagerDuty API

## Integration Points

### PagerDuty
- Adds incident notes with execution details
- Automatically resolves incidents on successful re-runs
- Preserves incident state on failed re-runs for manual intervention

### Rundeck
- Interacts with Rundeck API for execution management
- Retrieves execution metadata and logs
- Supports multi-project environments via dynamic project extraction

## Usage Example

### Manual Trigger
1. Navigate to the job in Rundeck UI
2. Enter the Execution ID of the job to re-run
3. Enter the PagerDuty Incident ID to update
4. Enter your PagerDuty user email
5. Verify the RBA URL is correct
6. Execute the job

### Automated Trigger (via PagerDuty Automation Actions)
- Configure PagerDuty Automation Action to trigger this job when incidents meet specific criteria
- Pass incident ID automatically via webhook payload
- Job handles re-run and incident updates autonomously

## Tags
`critical`, `pagerduty`, `automation`, `incident-management`, `job-execution`, `rerun`

## Notes
- This job uses workflow strategy ruleset to conditionally skip incident resolution on failures
- Error handling ensures workflow continues even if execution fails, allowing incident to be updated
- Execution logs are retrieved via HTTP API rather than native Rundeck logging for better formatting in PagerDuty notes

## Estimated Duration
2-5 minutes depending on the execution time of the re-run job

## Version History
- **v2**: Removed hardcoded personal values, parameterized email and tokens, updated naming conventions to kebab-case
- **v1**: Initial version with basic re-run functionality
