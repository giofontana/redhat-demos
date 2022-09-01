# Red Hat Advanced Cluster Management Demo

## Content:

1. Overview and observability
2. Search feature
3. Provision a cluster
4. Import an existing cluster
5. Policy creation and enforcement
6. Application deployment using GitOps

## Demo:

### Overview and observability

1. Navigate and explore the Overview feature
2. Explore Grafana

### Search

1. Explore the feature and try to search for a few things, such as:
- container: dns
- kind: persistentvolume
- kind: secrets

2. Save a search

### Provision a cluster

1. Create a cluster using ACM web interface

### Upgrade a cluster

1. When an upgrade is available you will see the "Upgrade available" link


### Import a cluster

1. Import using ACM web interface

### Policy creation and enforcement

1. Build a policy with the following information:
    - Name: policy-grc-namespace
    - Namespace: default
    - Specifications: CertificatePolicy -cert management expiration
    - Placement: env=prod

    Leave everything else as default and click Create. Please note that initially it will complain that there is an issue with the policy but shortly after should go green and get a checkmark

2. Explore the feature, example:
    - Name: policy-gitops-infra
    - Namespace: default
    - Specifications: policy-namespace - Must have Namespace gitops-infra
    - Placement: vendor=OpenShift

    Leave everything else as default and click Create. Please note that initially it will complain that there is an issue with the policy but shortly after should go green and get a checkmark

3. Create a policy to install openshift-gitops:

```
oc login ...
oc apply -f https://raw.githubusercontent.com/giofontana/redhat-demos/main/rhacm/policies/openshift-gitops-installed.yaml
```

### Application deployment using GitOps

1. Run the following command to create the GitOpsCluster object

```
oc apply -f https://raw.githubusercontent.com/giofontana/redhat-demos/main/rhacm/deployment/gitopscluster.yaml
```

2. From the web app create an ApplicationSet with the following parameters:
    - Name: book-import-argo
    - Argo server: openshift-gitops
    - URL: https://github.com/jnpacker/application-samples/
    - Revision: main
    - Path: book-import
    - Remote namespace: book-import
    - Sync policy: leave as-is
    - Placement: env=prod

3. Show ArgoCD and app on ACM
