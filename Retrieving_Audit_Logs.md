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



