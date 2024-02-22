# Deploy

```
helm repo add stable https://charts.onwalk.net/ || true
helm repo up
helm upgrade --install itsm-dev stable/itsm -n itsm-dev
```

```
export namespace=itsm-dev

echo "Create redis-password secret"
kubectl delete secret generic redis-secret -n  $namespace || true
kubectl create secret generic redis-secret                                     \
          --from-literal=redis-password="redis"                                \
          -n  $namespace

echo "Create mongodb-password secret"
kubectl delete secret generic mongodb-secret -n  $namespace || true
kubectl create secret generic mongodb-secret                                   \
          --from-literal=mongoUrl="mongodb://novu:novu@mongodb:27017/"         \
          -n  $namespace

echo "Create novu secret"
kubectl delete secret generic novu-secret -n  $namespace || true
kubectl create secret generic novu-secret                                       \
          --from-literal=jwt-secret="xxxxxxx"                                   \
          --from-literal=store-encryption-key="xxxx"                            \
          -n  $namespace

echo "Create s3 secret"
kubectl delete secret generic s3-secret -n  $namespace || true
kubectl create secret generic s3-secret                                         \
          --from-literal=endpoint="http://localstack:4566"                      \
          --from-literal=bucketName="novu-local"                                \
          --from-literal=region="xx"                                            \
          --from-literal=accessKey="test"                                       \
          --from-literal=secretKey="test"                                       \
          -n  $namespace

echo "Create minio secret"
kubectl delete secret generic minio-secret -n  $namespace || true
kubectl create secret generic minio-secret                                      \
          --from-literal=root-user="test"                                       \
          --from-literal=root-password="test"                                   \
          -n  $namespace
```


```
cat > redis-values.yaml << EOF
apisix:
  enabled: false
novu:
  enabled: false
windmill:
  enabled: false
postgresql:
  enabled: false
minio:
  enabled: false
mongodb:
  enabled: false
redis:
  enabled: true
  nameOverride: "redis"
  architecture: standalone
  global:
    imageRegistry: ""
    redis:
      password: ""
  auth:
    enabled: true
    sentinel: false
    password: ""
  master:
    persistence:
      enabled: false
    resources:
      requests:
        memory: 100Mi
        cpu: 100m
      limits:
        cpu: "200m"
        memory: "300Mi"
EOF
helm upgrade --install redis stable/itsm -n itsm-dev -f redis-values.yaml
```

```

cat > postgresql-values.yaml << EOF
apisix:
  enabled: false
novu:
  enabled: false
windmill:
  enabled: false
minio:
  enabled: false
redis:
  enabled: false
mongodb:
  enabled: false
postgresql:
  enabled: true
  fullnameOverride: windmill-postgresql
  global:
    imageRegistry: ""
    postgresql:
      auth:
        postgresPassword: "windmill"
        username: "postgres"
        password: "windmill"
        database: "windmill"
  primary:
    persistence:
      enabled: false
    resources:
      requests:
        memory: 100Mi
        cpu: 100m
      limits:
        cpu: "200m"
        memory: "300Mi"
EOF
helm upgrade --install postgresql stable/itsm -n itsm-dev -f postgresql-values.yaml

```

```
cat > mongodb-values.yaml << EOF
apisix:
  enabled: false
novu:
  enabled: false
windmill:
  enabled: false
postgresql:
  enabled: false
minio:
  enabled: false
redis:
  enabled: false
mongodb:
  enabled: true
  nameOverride: "mongodb"
  architecture: standalone
  useStatefulSet: true
  global:
    imageRegistry: ""
  persistence:
    enabled: false
  auth:
    enabled: true
    rootUser: root
    rootPassword: "mongodb"
    usernames:
    - novu
    passwords:
    - novu
    databases:
    - novu-db
  persistence:
    enabled: false
  resources:
    requests:
      memory: 100Mi
      cpu: 100m
    limits:
      cpu: "200m"
      memory: "300Mi"
EOF
helm upgrade --install mongodb stable/itsm -n itsm-dev -f mongodb-values.yaml
```

```
cat > minio-values.yaml << EOF
apisix:
  enabled: false
novu:
  enabled: false
windmill:
  enabled: false
postgresql:
  enabled: false
redis:
  enabled: false
mongodb:
  enabled: false
minio:
  enabled: true
  nameOverride: minio
  mode: standalone
  persistence:
    enabled: false
  defaultBuckets: "my-bucket, novu-local"
  auth:
    rootUser: test
    rootPassword: test
  resources:
    requests:
      memory: 100Mi
      cpu: 100m
    limits:
      cpu: "300m"
      memory: "300Mi"
EOF
helm upgrade --install minio stable/itsm -n itsm-dev -f minio-values.yaml
```
