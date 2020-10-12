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

