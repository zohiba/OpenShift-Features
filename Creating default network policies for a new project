# Developer Features on OpenShift 4

## Creating default network policies for a new project

### Prerequisites

* A working OCP 4+ cluster
* Admin access

As a cluster administrator, you can modify the new project template to automatically include NetworkPolicy objects when you create a new project.

## Steps

1- Log in as a user with cluster-admin privileges.

2 - Generate the default project template:
```$ oc adm create-bootstrap-project-template -o yaml > template.yaml```

3 - Use a text editor to modify the generated template.yaml file by adding to the list of existing objects in the template file.

To restrict namespaces from accessing other namespaces, add the following:

```
- apiVersion: networking.k8s.io/v1
  kind: NetworkPolicy
  metadata:
    name: deny-other-namespaces
  spec:
    podSelector: null
    ingress:
      - from:
          - podSelector: {}
 ```


To allow namespaces to access other ones by default add: 

```
- apiVersion: networking.k8s.io/v1
  kind: NetworkPolicy
  metadata:
    name: allow-from-same-namespace
  spec:
    podSelector:
    ingress:
    - from:
      - podSelector: {}
- apiVersion: networking.k8s.io/v1
  kind: NetworkPolicy
  metadata:
    name: allow-from-openshift-ingress
  spec:
    ingress:
    - from:
      - namespaceSelector:
          matchLabels:
            network.openshift.io/policy-group: ingress
    podSelector: {}
    policyTypes:
    - Ingress
```


4 - The project template must be created in the openshift-config namespace. Load your modified template:
```$ oc create -f template.yaml -n openshift-config```

5 -Edit the project configuration resource using the web console or CLI.

   Using the web console:

      Navigate to the Administration → Cluster Settings page.

      Click Global Configuration to view all configuration resources.

      Find the entry for Project and click Edit YAML.

   Using the CLI:
   
        $ oc edit project.config.openshift.io/cluster


Update the spec section to include the projectRequestTemplate and name parameters, and set the name of your uploaded project template. The default name is project-request.

Project configuration resource with custom project template


    apiVersion: config.openshift.io/v1
    kind: Project
    metadata:
    ...
    spec:
      projectRequestTemplate:
        name: <template_name>
After you save your changes, create a new project to verify that your changes were successfully applied.
```$ oc new-project <project> ```
Replace <project> with the name for the project you are creating.
Confirm that the network policy objects in the new project template exist in the new project:


```$ oc get networkpolicy```

You should now see the new network policies you added to the objects list in the template.
