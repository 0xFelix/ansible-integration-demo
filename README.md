# ansible-integration-demo [windows]

This Tekton pipeline shows how to create a Windows 10 VirtualMachine with [kubevirt-tekton-tasks](https://github.com/kubevirt/kubevirt-tekton-tasks) and how to further configure it with [git-clone](https://hub.tekton.dev/tekton/task/git-clone) and [ansible-runner](https://hub.tekton.dev/tekton/task/ansible-runner) on an OpenShift 4.10 cluster.

## Preparing the cluster

Make sure to setup `oc` and login first. Make also sure that [Tekton CLI](https://tekton.dev/docs/cli) (`tkn`) is installed.

1. Install `OpenShift Pipelines` from OperatorHub
2. Install `OpenShift Virtualization` from OperatorHub and create `HyperConverged`
3. Create namespace/project `kubevirt` by running `oc new-project kubevirt`
4. Deploy [`kubevirt-tekton-tasks`](https://github.com/kubevirt/kubevirt-tekton-tasks) by running `make deploy` in the cloned repo
5. Install `ansible-runner` task by running `tkn hub install task ansible-runner`
6. Create `windows10-installer` pipeline for OpenShift 4.10 by running `oc create -f windows10-installer-ocp-4.10.yaml`
7. Run [`windows10-installer`](https://github.com/kubevirt/kubevirt-tekton-tasks/tree/main/examples/pipelines/windows10-installer) Pipeline to populate the Windows 10 boot source

## Setting up the pipeline

1. Run `oc create -f windows10-unattend-winrm.yaml`
2. Run `oc create -f windows10-with-sysprep.yaml`
3. Run `oc create -f create-win-vm-and-run-ansible-pipeline.yaml`

## Running the pipeline

1. Run `oc create -f create-win-vm-and-run-ansible-pipelinerun.yaml`
