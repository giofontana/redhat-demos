
# K8s manifests to test Object Volume Claim using Noobaa and OpenShift Data Foundation

1. Run the following command to create the photo album application:

```
oc apply -k odf-noobaa/
```

2. An Object Bucket Claim will create an S3 bucket automatically for you on Noobaa. Run the following command to get application url:

```
echo "http://$(oc get route photo-album -n demo -o jsonpath='{.spec.host}')"
```

3. Access the application using your browser and upload photos using the button `Select Photo` and then `Upload`.
