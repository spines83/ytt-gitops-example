
User is testing locally
```
echo "apiVersion: v1
kind: Service
metadata:
  name: nginx-a
spec:
  selector:
    app.kubernetes.io/name: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376" > base/component_a/service.yaml


ytt -f base \
    -f envs/user-local \
    -f variants/enclave_1

# Pipe this to `kubectl apply -f` to test locally
# Upon validation, push changes to a branch and open a merge request
```

Update Dev

```
export NEW_TAG="nginx:1.14.3"
yq -i ".image.component_a = \"${NEW_TAG}\"" envs/dev/version.yaml
yq -i ".image.component_b = \"${NEW_TAG}\"" envs/dev/version.yaml 


ytt -f base \
    -f envs/dev \
    -f variants/enclave_1
```

Promote Dev > Staging
```
cp envs/dev/version.yaml envs/staging/version.yaml

ytt -f base \
    -f envs/staging \
    -f variants/enclave_2
```

Only update one component of dev (not a normal use case)
```
export NEW_TAG="nginx:1.14.5"
yq -i ".image.component_a = \"${NEW_TAG}\"" envs/dev/version.yaml

ytt -f base/component_a \
    -f envs/dev \
    -f variants/enclave_1
```

Roll the changes to staging 
```
cp envs/dev/version.yaml envs/staging/version.yaml

ytt -f base \
    -f envs/staging \
    -f variants/enclave_2
```

Roll the changes to production
```
cp envs/staging/version.yaml envs/production/version.yaml

ytt -f base \
    -f envs/production \
    -f variants/enclave_2
```

Revert everything
```
rm base/component_a/service.yaml
export OLD_TAG="nginx:1.14.2"
yq -i ".image.component_a = \"${OLD_TAG}\"" envs/dev/version.yaml
yq -i ".image.component_b = \"${OLD_TAG}\"" envs/dev/version.yaml 
cp envs/dev/version.yaml envs/staging/version.yaml
cp envs/staging/version.yaml envs/production/version.yaml
```