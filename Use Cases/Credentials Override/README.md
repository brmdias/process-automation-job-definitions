# Node Credentials Override

As per Rundeck documentation, credentials to login into nodes can be placed in Framework-level, Project-level, Node-level or Job-level.
Some organizations might require users to authenticate on nodes using their personal credentials, instead of a general automation user, to improve tracebility on who's doing what in their infrastructure.

Depending on the Node Executor Plugin in use, there are different ways to use a job option to prompt the password to the user.

Here are the documentated options available:
- [SSH Node Execution (Jsch node executor)](https://docs.rundeck.com/docs/manual/projects/node-execution/ssh.html#ssh-password-with-a-job-option)
- WinRM Node Executor (TBD).

# Example

On the job definition below, you can see an example of job options used to override node credentials for WinRM node executor.

[Job definition here](winrm_credentials_override.json)
