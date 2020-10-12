## Retrieving audit logs

### View the OpenShift Container Platform API server logs

<details> 
  <summary> OCP API Logs </summary> 
  

   To see all the log files for all the nodes:

   ```oc adm node-logs --role=master --path=openshift-apiserver/```

   To see all the log files for specific node:

   ```oc adm node-logs <master-0> --path=openshift-apiserver/```

   To see specifc log file associated with a node:

   ```oc adm node-logs <master-0> --path=openshift-apiserver/audit.log```

</details>

### View the Kubernetes API server logs

<details> 
  <summary> Kubernetes API Logs </summary> 

   To see all the log files for all the nodes:

   ```oc adm node-logs --role=master --path=kube-apiserver/```

   To see all the log files for specific node:

   ```oc adm node-logs <master-0> --path=kube-apiserver/```

   To see specifc log file associated with a node:

   ```oc adm node-logs <master-0> --path=kube-apiserver/audit.log```

</details>

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
  
  ``` oc adm node-logs <master-1> --path=kube-apiserver/audit.log | grep <project_name> | grep <deployment_name> | grep deployments | grep scale```
  
  ``` oc adm node-logs <master-2> --path=kube-apiserver/audit.log | grep <project_name> | grep <deployment_name> | grep deployments | grep scale```

<details> 
  <summary> Expected Output </summary> 

```
cpat@privatevnetbastion:~$ oc adm node-logs jcicost-shr2d-master-1 --path=kube-apiserver/audit.log | grep htlogs  | grep scale
{"kind":"Event","apiVersion":"audit.k8s.io/v1","level":"Metadata","auditID":"b603178c-ecbf-4fc4-a9a6-126d2dd9e6c1","stage":"ResponseComplete","requestURI":"/apis/apps/v1/namespaces/htlogs/deployments/htlogsdeployment/scale","verb":"patch","user":{"username":"kube:admin","groups":["system:cluster-admins","system:authenticated"],"extra":{"scopes.authorization.openshift.io":["user:full"]}},"sourceIPs":["172.20.219.4"],"userAgent":"oc/openshift (linux/amd64) kubernetes/b66f2d3","objectRef":{"resource":"deployments","namespace":"htlogs","name":"htlogsdeployment","apiGroup":"apps","apiVersion":"v1","subresource":"scale"},"responseStatus":{"metadata":{},"code":200},"requestReceivedTimestamp":"2020-10-12T22:44:12.631166Z","stageTimestamp":"2020-10-12T22:44:12.643025Z","annotations":{"authorization.k8s.io/decision":"allow","authorization.k8s.io/reason":"RBAC: allowed by ClusterRoleBinding \"cluster-admins\" of ClusterRole \"cluster-admin\" to Group \"system:cluster-admins\""}}
```
</details> 


- You can also delete the project from the console for example and repeat above steps to verify the user who deleted the project 

  ```oc adm node-logs <master-#> --path=kube-apiserver/audit.log | grep <project_name> |  grep project | grep delete```
  
  
  <details> 
  <summary> Expected Output </summary> 
  
   ```
   cpat@privatevnetbastion:~$ oc adm node-logs jcicost-shr2d-master-1 --path=kube-apiserver/audit.log | grep timing-test |  grep projects | grep delete
   
    {"kind":"Event","apiVersion":"audit.k8s.io/v1","level":"Metadata","auditID":"39382d96-7659-42d3-8b07-1f733e8f77c5","stage":"ResponseComplete","requestURI":"/apis/project.openshift.io/v1/projects/timing-test","verb":"delete","user":{"username":"zarintest","uid":"1f85e38f-fb32-440a-97db-abb59df549ce","groups":["system:authenticated:oauth","system:authenticated"],"extra":{"scopes.authorization.openshift.io":["user:full"]}},"sourceIPs":["172.20.219.4","10.128.2.3","10.129.0.35"],"userAgent":"Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:81.0) Gecko/20100101 Firefox/81.0","objectRef":{"resource":"projects","namespace":"timing-test","name":"timing-test","apiGroup":"project.openshift.io","apiVersion":"v1"},"responseStatus":{"metadata":{},"code":200},"requestReceivedTimestamp":"2020-10-12T22:27:05.950586Z","stageTimestamp":"2020-10-12T22:27:05.972000Z","annotations":{"authorization.k8s.io/decision":"allow","authorization.k8s.io/reason":"RBAC: allowed by RoleBinding \"admin/timing-test\" of ClusterRole \"admin\" to User \"zarintest\""}}
  ```


### Set up user account using htpasswd
<details> 
  <summary> Set up New User </summary> 
  
  
   1. Create or update your flat file with a user name and hashed password:
         
       ```htpasswd -c -B -b </path/to/users.htpasswd> <user_name> <password>```
  
   2. Continue to add or update credentials to the file:

      ```htpasswd -B -b </path/to/users.htpasswd> <user_name> <password>```

   3. To use the HTPasswd identity provider, you must define a secret that contains the HTPasswd user file.

       Create an OpenShift Container Platform Secret that contains the HTPasswd users file.

       ```oc create secret generic htpass-secret --from-file=htpasswd=</path/to/users.htpasswd> -n openshift-config```
       
   4. Configuring identity providers using the web console
      
        - Configure your identity provider (IDP) through the web console instead of the CLI.

        Prerequisites
        You must be logged in to the web console as a cluster administrator.
        
       a. Navigate to Administration → Cluster Settings.

       b. Under the Global Configuration tab, click OAuth.

       c. Under the Identity Providers section, select your identity provider from the Add drop-down menu.
  
</details>


Resources:
https://docs.openshift.com/container-platform/4.5/authentication/identity_providers/configuring-htpasswd-identity-provider.html
https://docs.openshift.com/container-platform/4.5/authentication/identity_providers/configuring-ldap-identity-provider.html
https://docs.openshift.com/container-platform/4.5/nodes/nodes/nodes-nodes-audit-log.html

