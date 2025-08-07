
# 🔍 Probes in Kubernetes

**Probes help Kubernetes check the health of your app inside a container.**

---

## 🔹 1. Readiness Probe
✅ “Am I ready to receive traffic?”

Kubernetes asks: “Can I send requests to this pod?”

If the answer is no, the pod is **temporarily removed** from the Service (not deleted).

📌 **Use case**:  
Your app takes time to start — you don’t want users to hit it until it's ready.

---

## 🔹 2. Liveness Probe
❤️ “Am I still alive?”

Kubernetes asks: “Is the app still working?”

If it fails, Kubernetes **restarts the container**.

📌 **Use case**:  
App gets stuck or hangs. Restarting fixes it.

---

## 🔹 3. Startup Probe
💤 “I’m just starting, give me more time!”

- Helps **slow-starting apps**
- Used instead of liveness/readiness **during startup**
- Prevents liveness from killing app too soon

📌 **Use case**:  
Java or .NET apps that take 1–2 minutes to boot.

---

## ✅ Simple Example YAML

```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 5

readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 5

startupProbe:
  httpGet:
    path: /start
    port: 8080
  failureThreshold: 30
  periodSeconds: 5
```

---

## 📊 Summary Table

| Probe Type    | Question Asked               | What Happens if It Fails?           |
|---------------|------------------------------|-------------------------------------|
| Readiness     | “Can I receive traffic?”     | Removed from Service                |
| Liveness      | “Am I healthy?”              | Container is restarted              |
| Startup       | “Am I still starting up?”    | Wait before running other probes    |

---

## 🧪 Full Probe YAML Breakdown

### ✅ Liveness Probe — checks if the app is alive

```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 5
```

| Field               | Meaning                                                       |
|---------------------|---------------------------------------------------------------|
| `httpGet.path`      | Probe sends HTTP GET request to `/healthz`                    |
| `port`              | Checks port `8080` inside the container                        |
| `initialDelaySeconds` | Wait 10 seconds after container starts before first check   |
| `periodSeconds`     | Repeat check every 5 seconds                                   |
| 👉 Behavior          | If `/healthz` fails, Kubernetes restarts the container        |

---

### ✅ Readiness Probe — checks if the app is ready to receive traffic

```yaml
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 5
```

| Field               | Meaning                                                       |
|---------------------|---------------------------------------------------------------|
| `httpGet.path`      | Probe sends HTTP GET to `/ready`                              |
| `initialDelaySeconds` | Starts checking after 5 seconds of container start          |
| `periodSeconds`     | Repeats every 5 seconds                                        |
| 👉 Behavior          | If `/ready` fails, pod is removed from the Service endpoint   |

---

### ✅ Startup Probe — gives app time to fully start before liveness kicks in

```yaml
startupProbe:
  httpGet:
    path: /start
    port: 8080
  failureThreshold: 30
  periodSeconds: 5
```

| Field               | Meaning                                                       |
|---------------------|---------------------------------------------------------------|
| `httpGet.path`      | Probe checks `/start` endpoint                                |
| `periodSeconds`     | Tries every 5 seconds                                          |
| `failureThreshold`  | Retries up to 30 times before declaring failure                |
| 👉 Behavior          | Gives 150s (30 × 5s) before other probes run                   |

> 🔧 While `startupProbe` is running, Kubernetes **pauses** the `livenessProbe`.

---

## 🔄 Timeline Example (Slow-starting app)

- **0s**: Pod starts  
- **0–150s**: Only `startupProbe` runs  
- If `/start` is OK → Kubernetes starts `liveness` and `readiness`  
- If `/start` fails 30 times → Pod is restarted

---

## 🧠 Why use all 3?

| Probe Type     | Role                                      |
|----------------|-------------------------------------------|
| `startupProbe` | Gives time to slow-starting apps          |
| `livenessProbe`| Keeps app running (auto-restart if frozen)|
| `readinessProbe`| Ensures only healthy pods get traffic    |
