## Retrieving audit logs

### View the OpenShift Container Platform API server logs

To see all the log files for all the nodes:

```oc adm node-logs --role=master --path=openshift-apiserver/```

To see all the log files for specific node:

```oc adm node-logs <master-0> --path=openshift-apiserver/```

To see specifc log file associated with a node:

```oc adm node-logs <master-0> --path=openshift-apiserver/audit.log```

### View the Kubernetes API server logs

To see all the log files for all the nodes:

```oc adm node-logs --role=master --path=kube-apiserver/```

To see all the log files for specific node:

```oc adm node-logs <master-0> --path=kube-apiserver/```

To see specifc log file associated with a node:

```oc adm node-logs <master-0> --path=kube-apiserver/audit.log```


### View username, useraction in auditlogs


- Login with a user

  ```oc login -u <username> -p <password> <api-URL>```
  

- Create a new project

  ```oc new-project <project_name>```

- Deploy sample ruby application

  ```oc new-app https://github.com/zohiba/hello-world  --name=<deployment_name> -n <project_name>```

- Scale the application
  
  ```oc scale deployment <deployment_name> --replicas=2```

- Now check kube-apiserver audit log for all 3 masters.

  ``` oc adm node-logs <master-0> --path=kube-apiserver/audit.log | grep <project_name> | grep <deployment_name> | grep deployments | grep scale```
  
  ```oc adm node-logs <master-1> --path=kube-apiserver/audit.log | grep <project_name> | grep <deployment_name> | grep deployments | grep scale```
  
  ```oc adm node-logs <master-2> --path=kube-apiserver/audit.log | grep <project_name> | grep <deployment_name> | grep deployments | grep scale```



