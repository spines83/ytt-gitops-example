
Update Dev

```
export NEW_TAG="nginx:1.14.3"
yq -i ".image.component_a = \"${NEW_TAG}\"" envs/dev/version.yaml
yq -i ".image.component_b = \"${NEW_TAG}\"" envs/dev/version.yaml 


ytt -f base \
    -f envs/dev
```

Promote Dev > Staging
```
cp envs/dev/version.yaml envs/staging/version.yaml

ytt -f base \
    -f envs/staging
```

Only update one component of dev
```
export NEW_TAG="nginx:1.14.5"
yq -i ".image.component_a = \"${NEW_TAG}\"" envs/dev/version.yaml

ytt -f base/component_a \
    -f envs/dev
```

Roll the changes to staging 
```
cp envs/dev/version.yaml envs/staging/version.yaml

ytt -f base \
    -f envs/staging
```

Roll the changes to production
```
cp envs/staging/version.yaml envs/production/version.yaml

ytt -f base \
    -f envs/production
```

Revert everything
```
export OLD_TAG="nginx:1.14.2"
yq -i ".image.component_a = \"${OLD_TAG}\"" envs/dev/version.yaml
yq -i ".image.component_b = \"${OLD_TAG}\"" envs/dev/version.yaml 
cp envs/dev/version.yaml envs/staging/version.yaml
cp envs/staging/version.yaml envs/production/version.yaml
```