# Global Variables

Global variables can be defined in the framework.properties file and can be used in your jobs, to refer to something specific to the Process Automation server.
This can be overriden in the project.properties file if needed.
Documentation [here](https://docs.rundeck.com/docs/administration/configuration/config-file-reference.html#global-execution-variables).

Example:
Configuration in the [framework.properties](https://docs.rundeck.com/docs/administration/configuration/config-file-reference.html#framework-properties) file
```yaml
# ----------------------------------------------------------------
# System-wide global variables.
# ----------------------------------------------------------------

# Expands to ${globals.environment}
framework.globals.environment = prod
```

This global variable can be used in your job definition as follows:

Workflow steps
![[Pasted image 20230831141316.png]]

Node filter
![[Pasted image 20230831141354.png]]

> **Note**
Even though it says "0 Nodes Matched" in Matched Nodes, it's because the GUI can't resolve the global variable value, but it is resolved during job execution and it will correctly filter your nodes.

# Example
Here's an example on my setup with two nodes: one prod and another as dev
![[Pasted image 20230831142839.png]]

On this environment, I created a global variable environment=dev on my framework.properties file:
![[Pasted image 20230831143206.png]]

Using the job from above, I get this output.
As you can see, it is only executing the command on node-1 (dev)
![[Pasted image 20230831143445.png]]

After changing the global variable value in framework.properties file to 'prod' and restart the cluster, I can validate that it is picking up node-2 as expected.
![[Pasted image 20230831144206.png]]

Job definition
![[Job_options_(using_global_vars).yaml]]