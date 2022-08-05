# **Meet The New Agent-Based OpenShift Installer**

<img src="mirror.jpg" style="width: 1000px;" border=0/>

OpenShift makes it easy to manage Kubernetes clusters that have unfettered internet access using well-known registries. However, preparation for deployment and maintenance of clusters reliant on locally hosted mirror registries can be challenging. The process of mirroring the registry content can be cumbersome with multiple steps, time consuming and sometimes down right frustrating.  However with the promise of the [oc-mirror](https://cloud.redhat.com/blog/how-oc-mirror-will-help-you-reduce-container-management-complexity) tool which was released as dev preview in OpenShift 4.10 a new dawn seamed on the horizon for mirroring activites. Now with the release of OpenShift 4.11 the oc-mirror tools has official gone GA and with adds additional feature functionality. 

## Use Cases We Are Addressing

* Minimizing the amount of content downloaded
* Simplifying the mirroring process
* Ensuring the right content is mirrored
* Better overall user experience

## Features Included

* Create bootable images with <code>openshift-install</code> command to deploy your clusters.
* In-place bootstrap, no extra node required.
* Works in fully disconnected deployments.
* Works with a mirrored local registry.
* Supports single node OpenShift (SNO), compact 3-node clusters, and highly available topologies.
* Can be automated with third party tools.
* User-friendly interface based on the Assisted Installer.



