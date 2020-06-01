
By default, the OpenShift Container Platform registry is secured during cluster installation so that it serves traffic through TLS. Unlike previous versions of OpenShift Container Platform, the registry is not exposed outside of the cluster at the time of installation.

1. Log into the installer node of the cluster (ssh sysadmin@<installerIP>)
2. Log into the master node from installer node (ssh core@<masterrIP>)
3. Log into the openshift cluster ( oc login https://api.srd.ocp.csplab.local:6443 -u ```<user>``` -p ```<password>```)
4. Run ```oc get nodes``` to make sure everything is running fine
5. To expose the registry using DefaultRoute:
  - Set DefaultRoute to True:
  
      ```oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge```
  
  - Log in with Podman:
  
      ```HOST=$(oc get route default-route -n openshift-image-registry --template='{{ .spec.host }}')```

      Do ```echo $HOST``` and make sure the result is same as the route from ```oc get routes -n openshift-image-registry```

      Run ```podman login -u $(oc whoami) -p $(oc whoami -t) --tls-verify=false $HOST```
      It should say login succeeded.
 6. To test that we can push and pull images using the exposed route:
 
    - To pull an image from openshift internal registry, we can check the available images using ```oc get images```
      And pull an image specifially from the internal registry
      
      ```podman pull $HOST/test1/spring-petclinic-git --tls-verify=false ```
      
    - To push an image to openshift internal registry, we can check available local images using ```podman images```
      If for example, we have nginx locally, we tag it using the format 
      
      ```podman tag image-name-that-we-want-to-push $HOST/project-name/image-name```
      
      ```podman push $HOST/project-name/image-name --tls-verify=false```
      
    If we do ```podman logout $HOST``` and try to repeat step 6, it should fail with ```unauthorized: authentication required``` error
 
