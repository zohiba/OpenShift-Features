# Developer Features on OpenShift 4

## How to enable/disable connectivity across projects(project namespaces) in an OpenShift 4 cluster

### Prerequisites

* A working OCP 4+ cluster

By default, all Pods in a project are accessible from other Pods and network endpoints. To enable/disable connectivity across projects, follow the steps below.

## Steps

1. Create two projects in the cluster.

     ![create2projects](images/create2projects.png)

2. Deploy application in each project.

     ![Deploy2Apps](images/Deploy2Apps.png)

     As the applications are getting build

      ![builds](images/Builds.png)

3. Open the pod terminal for both the applications

     ![accessPods](images/accessPods.png)

4. Test connectivity of a service within the same namespace.

     ![serviceInSameNS](images/serviceInSameNS.png)

5. Test connectivity of a service in another namespace. By default service in project-1 should be accessible from project-2. 

    But there is a catch. If we try the using the same convention, it fails

     ![serviceInDiffNSfailed](images/serviceInDiffNSfailed.png)

     Instead, we have to call the service using ```<servicename>.<namespacename>```

     ![serviceInDiffNSPass](images/serviceInDiffNSPass.png)

    It is also possible to access the services using ClusterIP but it is better to avoid it since the ClusterIP is dynamically generated and can change as the pod stops and starts.

6. To restrict access of a service in another project we need to create a network policy 

    Click create network policy

     ![networkPolicy](images/networkPolicy.png)

    Select the one that denies access to other namespaces

     ![createNetPolicy](images/createNetPolicy.png)
    
7. Test connectivity of a service in another namespace. It should not be able to access it now.

     ![accessPodafterNP](images/accessPodafterNP.png)

8. Test connectivity of a service within the same namespace. It should still be able to access it.

     ![withSameNSafterNP](images/withSameNSafterNP.png)

Note: We can customize the network polices to allow access to different projects and deny access to others. The following documentation provides further guidance on that https://docs.openshift.com/container-platform/4.3/networking/configuring-networkpolicy.html.
