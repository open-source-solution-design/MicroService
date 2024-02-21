# Deploy

```
helm repo add stable https://charts.onwalk.net/ || true
helm repo up
helm upgrade --install itsm-dev stable/itsm -n itsm-dev
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
