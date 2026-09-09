# Troubleshooting Runbook

## 1. API se ne pokreće

### Simptomi
- API pod je u statusu `CrashLoopBackOff`.
- Endpoint `http://localhost:8080/healthz` nije dostupan.

### Provjera

```bash
kubectl get pods -n ticketing
kubectl logs -l app=api -n ticketing
kubectl describe pod -l app=api -n ticketing
```

### Rješenje

- Provjeriti Docker image.
- Provjeriti ConfigMap i Secret.
- Ponovno pokrenuti deployment:

```bash
kubectl rollout restart deployment/api -n ticketing
```

---

# 2. PostgreSQL nije dostupan

### Simptomi

- API vraća grešku:
- relation "ticket_orders" does not exist

### Provjera

```bash
kubectl get pods -n ticketing
kubectl logs -l app=postgres -n ticketing
kubectl get pvc -n ticketing
```

### Rješenje

- Provjeriti je li PostgreSQL pod pokrenut.
- Provjeriti je li PVC u statusu Bound.
- Provjeriti postoji li tablica ticket_orders.

---

# 3. Redis nije dostupan

### Simptomi

Worker ne obrađuje narudžbe.

### Provjera

```bash
kubectl logs -l app=worker -n ticketing
kubectl logs -l app=redis -n ticketing
```

### Rješenje

Ponovno pokrenuti Redis:

```bash
kubectl rollout restart deployment/redis -n ticketing
```

---

# 4. Frontend ne prikazuje događaje

### Provjera

```bash
http://localhost:8080/events
http://localhost:3000/config
```

### Rješenje

Provjeriti vrijednost `API_BASE_URL`.

Ako je potrebno:

```bash
kubectl rollout restart deployment/frontend -n ticketing
```

---

# 5. Rolling Update i Rollback

Rolling update:

```bash
kubectl rollout restart deployment/api -n ticketing
```

Rollback:

```bash
kubectl rollout undo deployment/api -n ticketing
```

Provjera:

```bash
kubectl rollout status deployment/api -n ticketing
```