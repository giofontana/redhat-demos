
# K8s manifests to test Persistent Volume creation from OpenShift Data Foundation StorageClasses

1. Change the mysql-cephfd/kustomization.yaml and mysql-cephrbd/kustomization.yaml to reflect your image registry. Example:

```
images:
- name: mysql-image
  newName: registry.redhat.io/rhel8/mysql-80 # CHANGE HERE
  newTag: latest # CHANGE HERE
```

2. Run the following command to test CephFS PV. A new project named mysql-cephfs will be created with a mysql instance using a CephFS PVC.

```
oc apply -k mysql-cephfs/
```

3. Run the following command to test CephFS PV. A new project named mysql-cephfs will be created with a mysql instance using a CephRBD PVC.

```
oc apply -k mysql-cephrbd/
```
