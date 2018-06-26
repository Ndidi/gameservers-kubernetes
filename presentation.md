![original](images/title.jpg)
# <br/>
# Running game servers<br/>on Kubernetes
## Mo Firouz

---
![original](images/bg.jpg)

# About me
- My name is Mo(hammad) Firouz!
- Originally Iranian - can speak Farsi with a funny accent.
- Software architect
<br/>
- Co-founder of Heroic Labs

---
![original](images/bg.jpg)

# Heroic Labs

![right 50%](images/nakama-logo.png)

- Nakama, a realtime and social server for games.

- Realtime, server-authoritative multiplayer
- Flexible matchmaking system
- Extendable using Lua scripting
- Friends, Clans, Chat + more
<br />
- Completely open-source (and free!):
[https://github.com/heroiclabs/nakama](https://github.com/heroiclabs/nakama)

---
![original](images/bg.jpg)

# Things to discuss

1. Intro to Kubernetes
2. System overview
3. Two examples of game servers
    - Nakama server
    - Unity Headless servers

---
![original](images/bg.jpg)

# Intro to Kubernetes

^ Show of hands how many people heard-of/have used Kubernetes before?

^ Imperative and Declarative management

> _System for automating deployment, scaling, and management of **containerized** applications_

- Hardware provisioning - replaces Puppet, Chef and Ansible.
- Supervisor-like responsibilities - healthcheck and uptime
- Log aggregation + metrics + scheduled jobs + (much) more

---
![original](images/bg.jpg)

# Intro to Kubernetes

Some rules to follow:

1. Log to stdout and stderr
2. Return non-zero for abnormal exits
3. Add healthcheck endpoint - ideally to `/`
4. Add readiness endpoint - ideally to same as healthcheck
5. Use cmd args and env variables, not config files

---
![original](images/bg.jpg)

# Intro to Kubernetes

Kubernetes components

1. Pod
2. Controllers
3. Service + Ingress
4. Nodepools
5. Autoscaling

---
![original](images/bg.jpg)

# Pod

![right 85%](images/pods.png)

- Wrap one or more containers
- Pods are ephemeral
  - can be dynamically created / deleted
- Unique network IP (but not permanent)
- Storage resource(s)

---
![original](images/bg.jpg)

# Pod

```yml
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: nakama
spec:
  volumes:
  - name: nakama-volume
    emptyDir:
containers:
  - name: nakama
    image: heroiclabs/nakama
    command: "/bin/sh exec nakama"
    ports:
    - containerPort: 7350
      name: api
    volumeMounts:
    - mountPath: /nakama-data
      name: nakama-volume
```

---
![original](images/bg.jpg)

# Service

- A way to access pods
- Services have DNS names
  - `controller.namespace.service.cluster.local`
  - `controller.namespace`
- Expose applications internally and externally

---
![original](images/bg.jpg)

# Service

- ClusterIP: cluster-internal IP
- NodePort: Expose port (mapping) on the physical node
- LoadBalancer: Cloud provider’s load balancer
- ExternalName: CNAME without any proxying

---
![original](images/bg.jpg)

# Service

```yml
apiVersion: v1
kind: Service
metadata:
  name: nakamaservice
  labels:
    service: nakama
spec:
  selector:
    app: nakama
  type: NodePort
  ports:
  - port: 80
    targetPort: 7350
    name: api
```

---
![original](images/bg.jpg)

# Ingress

- Very similar to LoadBalancer service
- Only allowing HTTP(S) traffic into the cluster
- SSL Termination
- Usually configures Cloud Provider traffic

---
![original](images/bg.jpg)

# Ingress

```yml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: nakamaingress
  labels:
    ingress: nakama
  annotations:
    kubernetes.io/ingress.allow-http: "false"
spec:
  tls:
    - secretName: ingress-cert-secret
  backend:
    serviceName: nakama
    servicePort: 80
```

---
[.autoscale: true]

![original](images/bg.jpg)

# Controllers

- Replicating homogeneous set of pods
- Ensures healthy pods are running
- Deployment:
  + Manage infrastructure state from current to desired
  + Rolling update
  + Scale up/down pod replicas
  + History and rollback

---
![original](images/bg.jpg)

# Controller

- StatefulSet:
  + Very similar to Deployment
  + Sticky pod IDs - sequential gurantueed ordering
  + Disk and Network resources will stay the same across roll outs
<br />
- DaemonSets, (Cron)Jobs, and more...

---
![original](images/bg.jpg)

# Controller

```yml
apiVersion: apps/v1beta2
kind: StatefulSet
metadata:
  name: nakamastatefulset
spec:
  serviceName: "nakamaservice" # This must exist before Statefulset is created
  revisionHistoryLimit: 1
  replicas: 2
  podManagementPolicy: OrderedReady # or Parallel
  updateStrategy:
    type: RollingUpdate
  selector:
    matchLabels:
      app: nakama # this must matches below
  template:
    metadata:
      labels:
        app: nakama
    # ...rest of pod template...
```

---
[.autoscale: true]
![original](images/bg.jpg)

# Node Pool

- Assign special pods to special nodes

```yml
nodeSelector:
  disktype: ssd
```

- Use `affinity` and `anti-affinity` to indicate soft/hard requirement for node attractiveness
- Use `taint` and `tolerations` to repel pods from nodes
- Use both to schedule pods only on nodes with special hardware
- Ensure to use resource `requests` and `limits` for pods to limit noisy neighbour problem.

---
![original](images/bg.jpg)

# Node Pool

```yml
affinity:
  podAntiAffinity:
    preferredDuringSchedulingIgnoredDuringExecution:
    - weight: 100
      podAffinityTerm:
        topologyKey: kubernetes.io/hostname
        labelSelector:
          matchExpressions:
          - key: app
            operator: In
            values:
            - nakama
```

---
![original](images/bg.jpg)

# Autoscaling

```yml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: nakamascaler
  labels:
    autoscaler: nakama
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: StatefulSet
    name: nakamastatefulset
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      targetAverageUtilization: 50
      name: cpu
```

---
![original](images/bg.jpg)

# System overview

![inline 100%](images/system-overview.png)

---
[.autoscale: true]
![original](images/bg.jpg)

# Nakama game server

- Realtime multiplayer
- Server-authoritative multiplayer
- Matchmaking
- Custom code runtime
<br />
- Presence system
- Friends, Groups/clans
- Realtime chat, notifications
- User accounts, authentication

---
![original](images/bg.jpg)

# Nakama game server

![inline 100%](images/nakama-overview.png)

---
![original](images/bg.jpg)

# Nakama game server

![inline 85%](images/nakama-kube-pods.jpg)

---

![original](images/bg.jpg)

# Nakama game server

![inline 90%](images/nakama-kube-services.jpg)

---

![original](images/bg.jpg)

# Unity headless server

---

![original](images/bg.jpg)

# Unity headless server
