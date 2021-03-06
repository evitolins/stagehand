

Stagehand
=========

A simple UI for viewing & manipulating remote staging servers.


**WORK IN PROGRESS**


How it Works
------------
Stagehand runs preset bash commands.  When used with care, these commands can 
be used in several ways to provide server feedback, issue batch services, or 
update a git repository.  It provides all of this, while allowing multiple
staging areas to both view and manipulate at once.

Stagehand was built to be run using Apache's VirtualHosts, each staging area 
assigned a different port.  A simple json config file is used to establish 
staging area views, and both stage specific and global commands.

Git repositories are recognized, and certain info about their status will be
displayed in the UI if detected within each stage's path.

Simply add a name, path, port and a list of commands for each staging
area within the 'config.json' file.

	"stages":
		[
			{
				"name": "beta",
				"path" : "/var/www/beta",
				"port" : "80",
				"cmds" : [
					"git fetch"
				]
			},
			{
				"name": "Bob's Sandbox",
				"path" : "/var/www/sandbox_8080",
				"port" : "8080",
				"cmds" : [
					"git log --stat -1"
				]
			},			
			...


Stages
------
### Name
Name is used simply for display purposes

### Path
The path is used to set the CWD prior to issuing any command. This should
ensure that the command is running where it should be (providing the given 
command is not exposing a security risk).

Git queries are also made from this path.


### Port
We've chosen to use ports (via Apache Virtual Hosts) to divide each
staging area.  Currently the UI only utilzes the port data to provide
a convienient link to the stage's http:// location.


### Cmds
Commands are the power of Stagehand.  They provide direct shell access
quickly and easily.  They must be assigned and used with care, but with
careful usage, it can improve anyone's staging workflow.

There are 2 cmd scopes: Batch and Stage-Specific.  These will be explained
later on.


### Alerts
Alerts are similar to commands, but run automatically per staging area
listed.  These provide quick info about the staging area's current state.
An alert's behaves by displaying itself, if the "cmd_test" result is not
empty.

In some cases, the test is the same as what needs to return data.  To
avoid redundant shell commands, Stagehand will use "cmd_data" for both
the test and to display it's returned data ''if the "cmd_test" is not defined.

(Nesting alerts will come later, to streamline the feature.)


Cmds
----
### Batch
Batch cmds that can be run on all staging servers at once. It's typical 
for most batch commands to be simple, helpful queries.

### Stage-Specific
Stage-Specific cmds can be used to run more customized functions per
staging area.  It's also a good place to provide your more dangerous cmds. 


The first cmd listed in each scope is considered that scope's 'default'.

	"cmds" : [
		"ls -l",
		"git status --",
		"git log --stat -1"
	]

In this example, the default "ls -l" would be chosen to run, if the user
did not provide one.  You could keep the first command empty (""), to
avoid running commands on the initial page loads.


Cmds: Do's and Don'ts
---------------------
Guidelines for using cmds safely and effectively

- DO add simple commands to query useful/comparable info.
- DON'T use commands that change directory.
- DO add commands to help speed up your git interaction
- DON'T assign batch commands that could cause mass hysteria.
- DON'T use commands that have a "persistant" output, such as "top".

