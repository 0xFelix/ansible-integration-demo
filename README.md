# ansible-integration-demo [windows]

This Tekton pipeline shows how to create a Windows 10 VirtualMachine with [kubevirt-tekton-tasks](https://github.com/kubevirt/kubevirt-tekton-tasks) and how to further configure it with [git-clone](https://hub.tekton.dev/tekton/task/git-clone) and [ansible-runner](https://hub.tekton.dev/tekton/task/ansible-runner) on an OpenShift 4.10 cluster.

## Current status

This is currently not yet working as `wait-for-vmi-status` is not able to wait until the Windows 10 VM has fully booted and completed the basic setup and configuring remoting. This results in `ansible-runner` running before it can reach the VM so connecting will timeout.

```
[ansible-runner : run-playbook]
[ansible-runner : run-playbook] PLAY [all] *********************************************************************
[ansible-runner : run-playbook]
[ansible-runner : run-playbook] TASK [Gathering Facts] *********************************************************
[ansible-runner : run-playbook] fatal: [vm]: UNREACHABLE! => {"changed": false, "msg": "credssp: HTTPSConnectionPool(host='10.128.2.71', port=5986): Max retries exceeded with url: /wsman (Caused by ConnectTimeoutError(<urllib3.connection.VerifiedHTTPSConnection object at 0x7f64c266f610>, 'Connection to 10.128.2.71 timed out. (connect timeout=30)'))", "unreachable": true}
[ansible-runner : run-playbook]
[ansible-runner : run-playbook] PLAY RECAP *********************************************************************
[ansible-runner : run-playbook] vm                         : ok=0    changed=0    unreachable=1    failed=0    skipped=0    rescued=0    ignored=0
[ansible-runner : run-playbook]
```

## Preparing the cluster

Make sure to setup `oc` and login first. Make also sure that [Tekton CLI](https://tekton.dev/docs/cli) (`tkn`) is installed.

1. Install `OpenShift Pipelines` from OperatorHub
2. Install `OpenShift Virtualization` from OperatorHub and create `HyperConverged`
3. Create namespace/project `kubevirt` by running `oc new-project kubevirt`
4. Deploy [`tekton-tasks-operator`](https://github.com/kubevirt/tekton-tasks-operator) by running `make deploy` in the cloned repo
5. Create the TTO CR by running `oc create -f config/samples/tektontasks_v1alpha1_tektontasks.yaml` in the cloned repo
6. Install `ansible-runner` task by running `tkn hub install task ansible-runner`
7. Run `windows10-installer` Pipeline to populate the Windows 10 boot source

## Setting up the pipeline

1. Run `oc create -f windows10-unattend-winrm.yaml`
2. Run `oc create -f windows10-with-sysprep.yaml`
3. Run `oc create -f create-win-vm-and-run-ansible-pipeline.yaml`

## Running the pipeline

1. Run `oc create -f create-win-vm-and-run-ansible-pipelinerun.yaml`
