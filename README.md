# ansible-integration-demo

These Tekton pipelines shows how to create a Fedora VM running a Samba AD DC and how to join a Windows 10 VM to the domain. The [kubevirt-tekton-tasks](https://github.com/kubevirt/kubevirt-tekton-tasks) are used and further configuration is applied with [git-clone](https://hub.tekton.dev/tekton/task/git-clone) and [ansible-runner](https://hub.tekton.dev/tekton/task/ansible-runner). This example runs on OpenShift 4.10.

## Preparing the cluster

Make sure to setup `oc` and login first. Make also sure that [Tekton CLI](https://tekton.dev/docs/cli) (`tkn`) is installed.

1. Install `OpenShift Pipelines` from OperatorHub
2. Install `OpenShift Virtualization` from OperatorHub and create `HyperConverged`
3. Create namespace/project `kubevirt` by running `oc new-project kubevirt`
4. Deploy [`kubevirt-tekton-tasks`](https://github.com/kubevirt/kubevirt-tekton-tasks) by running `make deploy` in the cloned repo
5. Install `ansible-runner` task by running `tkn hub install task ansible-runner`
6. Create `windows10-installer` pipeline for OpenShift 4.10 by running `oc create -f https://raw.githubusercontent.com/0xFelix/ansible-integration-demo/windows/windows10-installer-ocp-4.10.yaml`
7. Run [`windows10-installer`](https://github.com/kubevirt/kubevirt-tekton-tasks/tree/main/examples/pipelines/windows10-installer) Pipeline to populate the Windows 10 boot source

## Setting up the pipelines

1. Run `oc create -f https://raw.githubusercontent.com/0xFelix/ansible-integration-demo/main/create-vm-and-run-ansible-pipeline.yaml`
2. Run `oc create -f https://raw.githubusercontent.com/0xFelix/ansible-integration-demo/main/fedora-with-pub-key.yaml`
3. Run `oc create -f https://raw.githubusercontent.com/0xFelix/ansible-integration-demo/windows/windows10-unattend-winrm.yaml`
4. Run `oc create -f https://raw.githubusercontent.com/0xFelix/ansible-integration-demo/windows/windows10-with-sysprep.yaml`
5. Run `oc create -f https://raw.githubusercontent.com/0xFelix/ansible-integration-demo/windows/create-win-vm-and-run-ansible-pipeline.yaml`

## Running the pipelines

1. Run `oc create -f create-vm-and-run-ansible-pipelinerun.yaml`
2. Run `oc create -f create-win-vm-and-run-ansible-pipelinerun.yaml`
