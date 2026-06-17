
# Github Refe URL: 
- https://github.com/iam-veeramalla/helm-zero-to-hero/

# Helm End-to-End Notes for DevOps Engineers

## 1. What is Helm?

**Helm** is a package manager for Kubernetes, similar to:

* `yum` → RedHat Linux
* `apt` → Ubuntu
* `maven` → Java dependencies
* **Helm** → Kubernetes applications

Helm helps you:

* Package Kubernetes manifests
* Deploy applications easily
* Upgrade and rollback releases
* Manage configurations across environments

---

## 2. Why Helm?

Without Helm:

* Need multiple YAML files

  * Deployment.yaml
  * Service.yaml
  * ConfigMap.yaml
  * Secret.yaml
  * Ingress.yaml

Managing these manually is difficult.

With Helm:

* All YAML files are packaged into a **Chart**
* Configuration is managed using **values.yaml**
* One command deploys everything

---

## 3. Helm Architecture

```text
Developer
    |
    v
Helm CLI
    |
    v
Kubernetes API Server
    |
    v
Kubernetes Cluster
```

**Components:**

* Helm CLI
* Helm Chart
* Kubernetes Cluster

> Note: Helm v3 removed Tiller (used in Helm v2).

---
# 4. Helm Terminologies:
| Term        | Description                     |
| ----------- | ------------------------------- |
| Chart       | Package of Kubernetes resources |
| Release     | Running instance of a chart     |
| Repository  | Storage for Helm charts         |
| Values.yaml | Configuration file              |
| Templates   | Kubernetes YAML templates       |

---
## What is a Helm Repository?
A **Helm Repository (Repo)** is a location where Helm charts are stored and shared.

It is similar to:
* **Maven Repository** → Stores JAR files
* **Docker Registry** → Stores Docker images
* **Helm Repository** → Stores Helm charts

A Helm repository contains packaged chart files (`.tgz`) and an `index.yaml` file.

### Example Helm Repositories

* Bitnami Helm repository
* Artifact Hub for discovering charts

### Add a Helm Repository:
```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
```

Update repositories:

```bash
helm repo update
```

List repositories:

```bash
helm repo list
```

Search charts in a repository:

```bash
helm search repo nginx
```

---
## What is a Helm Chart?
A **Helm Chart** is a package that contains Kubernetes manifests and templates required to deploy an application.

Think of it like:

| Technology | Package |
| ---------- | ------- |
| Java       | JAR/WAR |
| Docker     | Image   |
| Kubernetes | YAML    |
| Helm       | Chart   |

A chart bundles all Kubernetes resources such as:

* Deployment
* Service
* ConfigMap
* Ingress
* Secrets

---
## Helm Chart Structure:
```text
my-app/
├── Chart.yaml
├── values.yaml
├── templates/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── ingress.yaml
└── charts/
```

### Important Files:
**Chart.yaml** – Metadata about the chart:

```yaml
apiVersion: v2
name: my-app
version: 1.0.0
appVersion: "1.0"
```

**values.yaml** – Configurable values:

```yaml
replicaCount: 2

image:
  repository: nginx
  tag: latest
```

**templates/** – Kubernetes YAML templates:

```yaml
spec:
  replicas: {{ .Values.replicaCount }}
```

---
## Relationship Between Repo, Chart, and Release:
```text
Helm Repository
       ↓
   Helm Chart
       ↓
 helm install
       ↓
    Release
```

### Example:
1. Add repository:
```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
```

2. Install the Nginx chart:
```bash
helm install my-nginx bitnami/nginx
```

Where:
* **bitnami** → Repository name
* **nginx** → Chart name
* **my-nginx** → Release name

After installation, Kubernetes creates resources like Deployments and Services.

---
### What is the difference between Helm Repo and Helm Chart?
| Helm Repository             | Helm Chart                      |
| --------------------------- | ------------------------------- |
| Stores charts               | Package of Kubernetes resources |
| Similar to Maven repository | Similar to a JAR file           |
| Contains multiple charts    | Contains templates and values   |
| Example: Bitnami repo       | Example: nginx chart            |

**In one line:**
* **Helm Repo** = Place where charts are stored.
* **Helm Chart** = Package used to deploy an application on Kubernetes.

---
# 5. Install Helm:
### Linux

```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

### Verify Installation

```bash
helm version
```

Output:

```bash
version.BuildInfo{Version:"v3.x.x"}
```

---
# 6. Add Helm Repository:
Add Bitnami repository:

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
```

Update repositories:

```bash
helm repo update
```

List repositories:

```bash
helm repo list
```

Search charts:

```bash
helm search repo nginx
```

---
# 7. Install Application Using Helm:
Install Nginx:

```bash
helm install my-nginx bitnami/nginx
```

Check release:

```bash
helm list
```

Check Kubernetes resources:

```bash
kubectl get all
```

---
# 8. Helm Release Lifecycle:
```text
Install → Upgrade → Rollback → Uninstall
```

Commands:

### Install

```bash
helm install myapp chart-name
```

### Upgrade

```bash
helm upgrade myapp chart-name
```

### Rollback

```bash
helm rollback myapp 1
```

### Uninstall

```bash
helm uninstall myapp
```

---
# 9. Create Your Own Helm Chart:
Create chart:

```bash
helm create myapp
```

Directory structure:

```text
myapp/
│
├── Chart.yaml
├── values.yaml
├── charts/
└── templates/
    ├── deployment.yaml
    ├── service.yaml
    ├── ingress.yaml
    └── _helpers.tpl
```

---
# 10. Chart.yaml:
Contains chart metadata.

Example:

```yaml
apiVersion: v2
name: myapp
description: My Application Chart
type: application
version: 1.0.0
appVersion: "1.0"
```

---
# 11. values.yaml:
Stores configurable values.

Example:

```yaml
replicaCount: 2

image:
  repository: nginx
  tag: latest

service:
  type: ClusterIP
  port: 80
```

---
# 12. Deployment Template Example:
`templates/deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}
spec:
  replicas: {{ .Values.replicaCount }}

  selector:
    matchLabels:
      app: {{ .Chart.Name }}

  template:
    metadata:
      labels:
        app: {{ .Chart.Name }}

    spec:
      containers:
      - name: nginx
        image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
```

---
# 13. Service Template Example:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ .Release.Name }}
spec:
  type: {{ .Values.service.type }}

  ports:
  - port: {{ .Values.service.port }}

  selector:
    app: {{ .Chart.Name }}
```

---
# 14. Helm Variables:
| Variable         | Description        |
| ---------------- | ------------------ |
| `.Values`        | Access values.yaml |
| `.Release.Name`  | Release name       |
| `.Chart.Name`    | Chart name         |
| `.Chart.Version` | Chart version      |

Example:

```yaml
image: "{{ .Values.image.repository }}"
```

---
# 15. Helm Template Functions:
### IF Condition

```yaml
{{ if .Values.ingress.enabled }}
apiVersion: networking.k8s.io/v1
kind: Ingress
{{ end }}
```

### Range Loop

```yaml
{{ range .Values.env }}
- name: {{ .name }}
  value: {{ .value }}
{{ end }}
```

### Default Value

```yaml
{{ default "latest" .Values.image.tag }}
```

---
# 16. Dry Run Before Deployment:
Generate YAML:

```bash
helm template myapp .
```

Validate deployment:

```bash
helm install myapp . --dry-run
```

Debug:

```bash
helm install myapp . --dry-run --debug
```

---
# 17. Override Values:
Using command line:

```bash
helm install myapp . \
--set replicaCount=3 \
--set image.tag=v1
```

Using custom values file:

```bash
helm install myapp . -f dev-values.yaml
```

Example:

```text
values.yaml
dev-values.yaml
prod-values.yaml
```

---
# 18. Upgrade Application:
Change image version:

```bash
helm upgrade myapp . \
--set image.tag=v2
```

Verify:

```bash
kubectl get pods
```

---
# 19. View Release History:
```bash
helm history myapp
```

Output:

```text
REVISION    STATUS
1           deployed
2           deployed
3           failed
```

---
# 20. Rollback Release;
Rollback to revision 1:

```bash
helm rollback myapp 1
```

Check history:

```bash
helm history myapp
```

---

# 21. Package Helm Chart

Create package:

```bash
helm package myapp
```

Output:

```text
myapp-1.0.0.tgz
```

---
# 22. Push Chart to Repository:
You can store charts in:

* Nexus
* Artifactory
* Harbor
* GitHub Pages

Example:

```bash
helm push myapp-1.0.0.tgz oci://myrepo
```

---
# 23. OCI Registry Support:
Login:

```bash
helm registry login myrepo.com
```

Push:

```bash
helm push myapp-1.0.0.tgz oci://myrepo.com/charts
```

Pull:

```bash
helm pull oci://myrepo.com/charts/myapp
```

---
# 24. Helm with Jenkins Pipeline:
Example:

```groovy
stage('Helm Deploy') {
    steps {
        sh '''
        helm upgrade --install myapp . \
        -f values-dev.yaml \
        --namespace dev
        '''
    }
}
```

---
# 25. Real-Time DevOps Workflow:
```text
Developer commits code
        ↓
GitHub
        ↓
Jenkins Pipeline
        ↓
Build Docker Image
        ↓
Push to Nexus/Registry
        ↓
Update Helm values.yaml
        ↓
Helm Upgrade
        ↓
Kubernetes Deployment
```

---
# 26. Important Helm Commands Cheat Sheet:
```bash
helm version
helm repo add
helm repo update
helm search repo nginx
helm create myapp
helm install myapp .
helm list
helm history myapp
helm upgrade myapp .
helm rollback myapp 1
helm uninstall myapp
helm template myapp .
helm package myapp
```

---
### Q1: What is Helm?

**Answer:** Helm is a package manager for Kubernetes used to deploy and manage applications using charts.

### Q2: What is a Chart?

**Answer:** A chart is a package containing Kubernetes manifests and templates.

### Q3: What is a Release?

**Answer:** A deployed instance of a Helm chart.

### Q4: Difference between Helm v2 and v3?

**Answer:** Helm v3 removed Tiller and improved security.

### Q5: How do you rollback a deployment?

```bash
helm rollback <release-name> <revision>
```

---

# 28. DevOps Engineer Best Practices

✅ Use separate values files for Dev, QA, and Prod
✅ Store charts in Nexus/Harbor
✅ Use `helm lint` before deployment
✅ Use `helm template` for validation
✅ Enable rollback strategy
✅ Version charts properly

Useful commands:

```bash
helm lint .
helm template .
helm dependency update
helm get values myapp
helm get manifest myapp
```

This covers Helm from beginner to advanced level, including real-time DevOps and CI/CD integration.

