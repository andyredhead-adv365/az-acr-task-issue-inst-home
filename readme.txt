Reproduce the issue where a container instance started from a "cmd" 
instruction in an ACR multi-step yaml build has the value of $HOME changed and 
the contents of .ssh overridden.

Assumes:
 - you are working on a command line with az logged in
 - you have an ACR instance already set up

Steps:
 1. review the contents of tasks.yaml - not no container images are built, only an as-is instance of ubuntu:16.04 is started
 2. create an ACR task that references the build steps defined in the demo git repo
 3. run the build pipeline
 4. observe the output


Step 1)

tasks.yaml contains:

version: v1.0.0
steps:
  - cmd: -t ubuntu:16.04 echo $HOME
    id: testacrbuild
    detatch: false

NOTE: the -cmd indicates ubuntu:16.04 and directly invokes echo with the value of the HOME env var.


Step 2)

az acr task create \
    --context https://github.com/andyredhead-adv365/az-acr-task-issue-inst-home.git \
    --name AcrTaskIssueInstHome \
    --registry your-acr-name \
    --file tasks.yaml \
    --commit-trigger-enabled false \
    --pull-request-trigger-enabled false \

i.e.

az acr task create \
    --context https://github.com/andyredhead-adv365/az-acr-task-issue-inst-home.git \
    --name AcrTaskIssueInstHome \
    --registry a365pkg \
    --file tasks.yaml \
    --commit-trigger-enabled false \
    --pull-request-trigger-enabled false


Step 3)

az acr task run --registry your-acr-name --name AcrTaskIssueInstHome

i.e.

az acr task run --registry a365pkg --name AcrTaskIssueInstHome


Step 4)

view the output in the console

or 

az acr task logs --registry your-acr-name


Results)

The expected result would be to see the value of $HOME be set to 

    /root

Actually see

    /acb/home