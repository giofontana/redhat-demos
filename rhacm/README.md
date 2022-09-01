# Red Hat Advanced Cluster Management Demo

## Content:

1. Provision a cluster
2. Import an existing cluster
3. Policy creation and enforcement
4. Application deployment using GitOps
5. Explore observability

## Preparation:

1. Provision 2 single node clusters with label env=prod
2. Outside ACM provision an OCP cluster: use https://github.com/sa-ne/openshift-github-actions
3. Install observability:

```
S3_BUCKET_NAME=grafana-gfontana
AWS_ACCESS_KEY=<your-aws-access-key>
AWS_SECRET_KEY=<your-aws-secret>

aws configure
aws s3 mb s3://$S3_BUCKET_NAME

oc create namespace open-cluster-management-observability

DOCKER_CONFIG_JSON=`oc extract secret/pull-secret -n openshift-config --to=-`

oc create secret generic multiclusterhub-operator-pull-secret -n open-cluster-management-observability --from-literal=.dockerconfigjson="$DOCKER_CONFIG_JSON" --type=kubernetes.io/dockerconfigjson

cat <<EOF | oc apply -f - 
apiVersion: v1
kind: Secret
metadata:
  name: thanos-object-storage
  namespace: open-cluster-management-observability
type: Opaque
stringData:
  thanos.yaml: |
    type: s3
    config:
      bucket: $S3_BUCKET_NAME
      endpoint: s3.amazonaws.com
      insecure: false
      access_key: $AWS_ACCESS_KEY
      secret_key: $AWS_SECRET_KEY
EOF

cat <<EOF | oc apply -f -
apiVersion: observability.open-cluster-management.io/v1beta2
kind: MultiClusterObservability
metadata:
  name: observability #Your customized name of MulticlusterObservability CR
  namespace: open-cluster-management-observability
spec:
  availabilityConfig: High # Available values are High or Basic
  imagePullPolicy: Always
  imagePullSecret: multiclusterhub-operator-pull-secret
  observabilityAddonSpec: # The ObservabilityAddonSpec defines the global settings for all managed clusters which have observability add-on enabled
    enableMetrics: true # EnableMetrics indicates the observability addon push metrics to hub server
    interval: 60 # Interval for the observability addon push metrics to hub server
  retentionResolution1h: 30d # How long to retain samples of 1 hour in bucket
  retentionResolution5m: 14d
  retentionResolutionRaw: 5d
  storageConfig: # Specifies the storage to be used by Observability
    metricObjectStorage:
      name: thanos-object-storage
      key: thanos.yaml
    statefulSetSize: 10Gi # The amount of storage applied to the Observability StatefulSets, i.e. Amazon S3 store, Rule, compact and receiver.
    storageClass: gp2
EOF
```