In a Helm chart, **`Chart.yaml`**, **`values.yaml`**, and **`templates/`** are the three most important components.

---
# 1. `Chart.yaml`
This file contains **metadata** about the Helm chart.

Think of it as the **identity card** of the chart.

### Example:
```yaml
apiVersion: v2
name: nginx-chart
description: A Helm chart for Nginx
type: application
version: 1.0.0
appVersion: "1.25"
```

### Explanation:
| Field         | Description              |
| ------------- | ------------------------ |
| `apiVersion`  | Helm chart API version   |
| `name`        | Chart name               |
| `description` | Description of the chart |
| `type`        | application or library   |
| `version`     | Chart version            |
| `appVersion`  | Application version      |

### Example:

* Chart version: `1.0.0`
* Nginx version: `1.25`

---
# 2. `values.yaml`
This file stores **configurable values**.

Instead of hardcoding values in YAML files, we keep them in `values.yaml`.

### Example:
```yaml
replicaCount: 2

image:
  repository: nginx
  tag: latest

service:
  type: ClusterIP
  port: 80
```

### Purpose:
* Change replicas
* Change Docker image
* Change service type
* Environment-specific configurations

---
## Accessing Values in Templates:
Helm uses:

```yaml
{{ .Values.variable_name }}
```

Example:

```yaml
replicas: {{ .Values.replicaCount }}
```

Output:

```yaml
replicas: 2
```

---
# 3. `templates/`
The `templates/` folder contains **Kubernetes YAML templates**.

These templates use values from `values.yaml` and generate actual Kubernetes manifests.

### Example:

`templates/deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}
spec:
  replicas: {{ .Values.replicaCount }}

  template:
    spec:
      containers:
      - name: nginx
        image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
```

If `values.yaml` contains:

```yaml
replicaCount: 3

image:
  repository: nginx
  tag: 1.25
```

Helm generates:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3

  template:
    spec:
      containers:
      - name: nginx
        image: nginx:1.25
```

---
# Complete Flow:
```text
values.yaml
      ↓
templates/*.yaml
      ↓
helm install
      ↓
Generated Kubernetes YAML
      ↓
Deployed to Kubernetes
```

---
# Real-Time Example:
Suppose you have:

### values.yaml
```yaml
replicaCount: 2
```

### deployment.yaml
```yaml
spec:
  replicas: {{ .Values.replicaCount }}
```

Run:

```bash
helm install my-app .
```

Helm replaces:

```yaml
{{ .Values.replicaCount }}
```

with:

```yaml
2
```

and deploys the application.

---
**Q: What are `Chart.yaml`, `values.yaml`, and `templates` in Helm?**

**Answer:**
* **`Chart.yaml`** contains chart metadata such as name and version.
* **`values.yaml`** stores configurable values for the application.
* **`templates/`** contains Kubernetes manifest templates that use values from `values.yaml` to generate actual YAML files during deployment.

### Easy Way to Remember:
```text
Chart.yaml    → Chart Information
values.yaml   → Configuration Values
templates/    → Kubernetes YAML Templates
```
