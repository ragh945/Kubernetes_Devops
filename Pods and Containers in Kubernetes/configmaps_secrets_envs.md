
# 📦 ConfigMaps, Secrets, and Volumes in Kubernetes (Simple Explanation)

Kubernetes provides ways to manage app configuration, credentials, and persistent data using **ConfigMaps**, **Secrets**, and **Volumes**.

---

## 🔹 What is a ConfigMap?

A **ConfigMap** is used to store **non-sensitive configuration data** in key-value pairs.

### ✅ Example Use Case:
Store things like:
- App settings (`APP_MODE=production`)
- URLs or feature flags

### 🧪 Simple YAML Example:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
data:
  APP_MODE: "production"
  LOG_LEVEL: "info"
```

---

## 🔒 What is a Secret?

A **Secret** is like a ConfigMap, but it stores **sensitive data**, like passwords, tokens, or API keys.

Secrets are **base64 encoded** and more secure than ConfigMaps.

### ✅ Example Use Case:
- Store database credentials
- Store API keys or TLS certificates

### 🧪 Simple YAML Example:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  DB_PASSWORD: c2VjdXJlcGFzcw==  # base64 for 'securepass'
```

---

## 📁 What are Volumes in Kubernetes?

A **Volume** is used to store data in a way that survives container restarts.

Volumes can be:
- EmptyDir (temporary)
- PersistentVolume (external storage)
- Or used to **inject ConfigMaps or Secrets into containers** as files

### ✅ Example Use Case:
- Persist user-uploaded files
- Load configs as files inside a container

---

## 🔄 Mounting ConfigMaps & Secrets into Pods

You can **inject** ConfigMaps and Secrets into containers:

### 1. As **Environment Variables**

```yaml
envFrom:
  - configMapRef:
      name: my-config
  - secretRef:
      name: my-secret
```

### 2. As **Files using Volumes**

```yaml
volumes:
  - name: config-vol
    configMap:
      name: my-config

volumeMounts:
  - name: config-vol
    mountPath: "/etc/config"
```

---

## 🔍 Difference Between ConfigMaps and Secrets

| Feature         | ConfigMap          | Secret             |
|----------------|--------------------|--------------------|
| Data Type      | Non-sensitive data | Sensitive data     |
| Storage Format | Plain text         | Base64 encoded     |
| Use Case       | App configs        | Passwords, tokens  |
| Security       | Low                | High               |

---

## 🧠 When to Use What?

| You Need To...                           | Use       |
|------------------------------------------|-----------|
| Store app settings, env flags            | ConfigMap |
| Store sensitive data (passwords, keys)   | Secret    |
| Persist files, configs, or user data     | Volume    |

---
