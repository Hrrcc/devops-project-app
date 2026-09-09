# Secure Event Ticketing Platform (Sample DevSecOps Project)

Ovaj repozitorij je referentni uzorak aplikacije za kolegij **Uvod u DevOps - DevSecOps**.
Prikazuje cijeli tok: lokalni razvoj kroz Compose i produkcijski deployment kroz Kubernetes manifeste.

## Arhitektura

- `frontend` - web UI za pregled evenata i kupnju karata
- `api` - REST API za evente, narudzbe i health provjere
- `worker` - pozadinska obrada queue poruka
- `postgres` - trajna pohrana narudzbi
- `redis` - queue/cache sloj

### Brza validacija funkcionalnosti

1. Health API:
   ```bash
   curl http://localhost:8080/healthz
   curl http://localhost:8080/readyz
   ```
2. Dohvati evente:
   ```bash
   curl http://localhost:8080/events
   ```
3. Posalji narudzbu:
   ```bash
   curl -X POST http://localhost:8080/tickets/purchase \
     -H "Content-Type: application/json" \
     -d '{"eventId":"evt-1001","customerEmail":"student@example.com","quantity":2}'
   ```
4. Provjeri obradene narudzbe:
   ```bash
   curl http://localhost:8080/tickets/orders
   ```
5. UI:
   - Otvori `http://localhost:3000`

## Sigurnosni elementi

- Multi-stage Docker build i non-root runtime korisnik
- Secret + ConfigMap odvojena konfiguracija
- Liveness/Readiness probe
- Resource requests/limits
- ServiceAccount + RBAC
- NetworkPolicy segmentacija
- Trivy skeniranje slika u CI pipelineu

## Sigurnosno skeniranje

Docker imageovi skenirani su pomoću Trivy alata.

Rezultati skeniranja nalaze se u mapi:
security-reports/

## Pokretanje s Docker Compose

```bash
docker compose up -d --build
```

Gašenje:

```bash
docker compose down
```

## Pokretanje na Kubernetesu

Primjena svih Kubernetes resursa:

```bash
kubectl apply -f infra/kubernetes/
```

Provjera:

```bash
kubectl get pods -n ticketing
```

## Rolling Update

```bash
kubectl rollout restart deployment/api -n ticketing
```

## Rollback

```bash
kubectl rollout undo deployment/api -n ticketing
```

## Autor

Projekt izrađen za kolegij Uvod u DevOps – DevSecOps.