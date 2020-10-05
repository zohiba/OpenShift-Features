# Upgrade OCP 4.x cluster

**Note/Prerequisites:** One of the limitation you can find is not having direct upgrade paths and needing to make intermediate upgrades to reach the necessary versions, apart from that, it is recommended you always do an ETCD backup and carefully read the release notes of every version to be aware of new features and avoid unexpected changes:

[4.3 release notes](https://docs.openshift.com/container-platform/4.3/release_notes/ocp-4-3-release-notes.html?extIdCarryOver=true&sc_cid=701f2000001OH6kAAG)

[4.4 release notes](https://docs.openshift.com/container-platform/4.4/release_notes/ocp-4-4-release-notes.html?extIdCarryOver=true&sc_cid=701f2000001OH6kAAG)

- [Generic steps for upgrade](#Generic-steps-for-upgrade)
- [Can we Upgrade from OCP 4.2 to 4.4?](#Can-we-upgrade-from-ocp-42-to-44)
- [Troubleshooting](#troubleshooting-steps)

## Generic steps for upgrade:

- Login into your desired cluster via "oc" cli command:

  ```oc login -u <userid> -p <password> https://api.<clusterid>.<domain>:6443```
- (Optional but recommended) Confirm the general cluster status, no degraded or progressing operators, all pods running, all nodes ready, etc.

``` oc get clusterversion
    oc get clusteroperators
    oc get nodes -o wide
    oc get pods -A | grep -v "Running\|Completed\|Terminated"
```
- If the cluster status is ok, as expected, let's proceed to check which upgrade possibilities do we have on the fast-4.y channel (the target one) using this [script](https://github.com/pamoedom/ocp4upc), this will give us information about the maximum 4.x.z version that we should be running if we want to be able to proceed with the upgrade, because not always the latest 4.x version is upgradable, for example:

```./ocp4upc.sh 4.3.23```
  ```
  [INFO] Checking prerequisites (curl jq dot)... [OK] 
  [INFO] Errata provided (4.x.z mode), targeting '4.4' channels for upgrade path generation.
  [INFO] Checking if '4.3.23' (amd64) is a valid release... [OK] 
  [INFO] Result exported as 'stable-4.4_amd64_20200717.svg'
  [INFO] Result exported as 'fast-4.4_amd64_20200717.svg'
  ```
- Once determined which 4.x.z version we need to have in order to reach the desired 4.y (usually the latest one), let's proceed with the upgrade without changing your current 4.x channel (the 4.x.z versions showed in the fast-4.y channel are also available in your current stable-4.x channel at this point), for example:

```oc adm upgrade --to=4.x.z``` or recommended ```$ oc adm upgrade --to-latest```
- Watch the process and wait until the upgrade finishes

``` watch -n10 "oc get clusterversion && echo && oc get co && echo && oc get nodes -o wide"```

- (Important) Once the cluster is successfully upgraded to desired 4.x.z version, confirm again the cluster general status before continuing.

- (Required) Change now the cluster channel as follows (remember to properly change 4.y with your target version):

```oc patch clusterversion/version -p '{"spec":{"channel":"stable-4.y"}}' --type=merge```
- (Optional) Confirm the new upgrade paths visibility:

```oc adm release info
   oc adm upgrade
```

- Upgrade now to desired version 4.y.z: ```oc adm upgrade --to=4.y.z```
- Once the cluster is successfully upgraded to version 4.y.z, confirm again the cluster general status.


### Can we Upgrade from OCP 4.2 to 4.4


It's possible to upgrade from 4.2 to 4.4 but you'll need to do it in minimum 2 steps: from 4.2 to 4.3 and after that, from 4.3 to 4.4.

You can use the same [script](https://github.com/pamoedom/ocp4upc) to find out the best upgrade paths based on your source version and the target one, for example, let's suppose you have version 4.2.16:

```ocp4upc 4.2.16```
  ```
  [INFO] Checking prerequisites (curl jq dot)... [OK] 
  [INFO] Errata provided (4.x.z mode), targeting '4.3' channels for upgrade path generation.
  [INFO] Checking if '4.2.16' (amd64) is a valid release... [OK] 
  [WARN] Discarding channel 'fast-4.3_amd64', it doesn't differ from 'stable-4.3_amd64'.
  [INFO] Result exported as 'stable-4.3_amd64_20200728.svg'
  ```
This indicate that there is a direct path to 4.3.18 using stable-4.3, now let's check the 4.3.18 options:

```ocp4upc 4.3.18```
  ```
  [INFO] Checking prerequisites (curl jq dot)... [OK] 
  [INFO] Errata provided (4.x.z mode), targeting '4.4' channels for upgrade path generation.
  [INFO] Checking if '4.3.18' (amd64) is a valid release... [OK] 
  [INFO] Result exported as 'stable-4.4_amd64_20200728.svg'
  [INFO] Result exported as 'fast-4.4_amd64_20200728.svg'
  ```
This indicates that 4.3.18 can be upgraded directly to 4.4.9 using stable-4.4 channel and indirectly to latest 4.4.13 doing an intermediate jump to 4.3.28 for example.

**NOTE:** If your 4.2.x version is too old, then you will need to make a first jump within the same stable-4.2 channel to reach at least the aforementioned 4.2.16 version, you can check the same-minor versions paths calling the script as follows (please note the extra . at the end of the version):

```ocp4upc 4.2.0.```
  ```
    [INFO] Checking prerequisites (curl jq dot)... [OK] 
    [INFO] Errata provided (4.x.z. mode), targeting '4.2' channels for upgrade path generation.
    [INFO] Checking if '4.2.0' (amd64) is a valid release... [OK] 
    [INFO] Result exported as 'stable-4.2_amd64_20200728.svg'
    [INFO] Result exported as 'fast-4.2_amd64_20200728.svg'
  ```

After confirming an upgrade is possible, we can follow the upgrade procedure.



# Troubleshooting steps

- Cancel an upgrade once started using ```oc adm upgrade --clear```

- confirm which channel we have configured using ``` oc get clusterversion/version -o jsonpath={.spec.channel}```

- See release info using ```oc adm release info```

- Check which upgrades are available in the current channel (without upgrading) using  ```$ oc adm upgrade```
