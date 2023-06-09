---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: kubedoom
  namespace: kubedoom
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: kubedoom
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: kubedoom
    namespace: kubedoom
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kubedoom-deployment
  namespace: kubedoom
spec:
  replicas: 1
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 0
      maxUnavailable: 1
  selector:
    matchLabels:
      app: kubedoom-app
  template:
    metadata:
      labels:
        app: kubedoom-app
    spec:
      hostNetwork: true
      serviceAccountName: kubedoom
      containers:
      - image: storaxdev/kubedoom:0.5.0
        name: kubedoom
        ports:
        - containerPort: 5900
          protocol: TCP