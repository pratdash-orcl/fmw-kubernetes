+++
title = "Troubleshooting"
weight = 11
pre = "<b>11. </b>"
description = "How to Troubleshoot domain creation failure."
+++

#### Domain creation failure

If the OAM domain creation fails when running `create-domain.sh`, run the following to diagnose the issue:

1. Run the following command to diagnose the create domain job:

   ```bash
   $ kubectl logs <domain_job> -n <domain_namespace>
   ```
   
   For example:
   
   ```bash
   $ kubectl logs accessinfra-create-fmw-infra-sample-domain-job-c6vfb -n accessns
   ```
   
   Also run:

   ```bash
   $ kubectl describe pod <domain_job> -n <domain_namespace>
   ```   

   For example:
   
   ```bash
   $ kubectl describe pod accessinfra-create-fmw-infra-sample-domain-job-c6vfb -n accessns
   ```
   
   Using the output you should be able to diagnose the problem and resolve the issue. 
   
   Clean down the failed domain creation by following steps 1-4 in [Delete the OAM domain home]({{< relref "/oam/manage-oam-domains/delete-domain-home" >}}). Then 
   [recreate the PC and PVC]({{< relref "/oam/prepare-your-environment/#create-a-kubernetes-persistent-volume-and-persistent-volume-claim" >}}) then execute the [OAM domain creation]({{< relref "/oam/create-oam-domains" >}}) steps again.
   
2. If any of the above commands return the following error:

   ```bash
   Failed to start container "create-fmw-infra-sample-domain-job": Error response from daemon: error while creating mount source path
   '/scratch/OAMDockerK8S/accessdomainpv ': mkdir /scratch/OAMDockerK8S/accessdomainpv : permission denied
   ```
    
   then there is a permissions error on the directory for the PV and PVC and the following should be checked:
   
   a) The directory has 777 permissions: `chmod -R 777 <work directory>/accessdomainpv`.
   
   b) If it does have the permissions, check if an `oracle` user exists and the `uid` and `gid` equal `1000`, for example:
   
   ```bash
   $ uid=1000(oracle) gid=1000(spg) groups=1000(spg),59968(oinstall),8500(dba),100(users),1007(cgbudba)
   ```
   
   Create the `oracle` user if it doesn't exist and set the `uid` and `gid` to `1000`.
   
   c) Edit the `<work directory>/weblogic-kubernetes-operator/kubernetes/samples/scripts/create-access-domain-pv-pvc/create-pv-pvc-inputs.yaml` and add a slash to the end of the directory for the `weblogicDomainStoragePath` parameter:
   
   ```bash
   weblogicDomainStoragePath: /scratch/OAMDockerK8S/accessdomainpv/
   ```
   
   Clean down the failed domain creation by following steps 1-4 in [Delete the OAM domain home]({{< relref "/oam/manage-oam-domains/delete-domain-home" >}}). Then 
   [recreate the PC and PVC]({{< relref "/oam/prepare-your-environment/#create-a-kubernetes-persistent-volume-and-persistent-volume-claim" >}}) and then execute the [OAM domain creation]({{< relref "/oam/create-oam-domains" >}}) steps again.