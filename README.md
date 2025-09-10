# Kubebuilder CronJob Controller 


---

## Prerequisites

* Go (≥1.21)
* Kubebuilder
* Docker
* kubectl
* Kind or another Kubernetes cluster

---

## 1. Create a Project

```bash
mkdir project
cd project
kubebuilder init --domain tutorial.kubebuilder.io --repo tutorial.kubebuilder.io/project
```

---

## 2. Create an API

```bash
kubebuilder create api --group batch --version v1 --kind CronJob
```

Edit API type definitions in:

```
api/v1/cronjob_types.go
```

---

## 3. Generate and Update Manifests

```bash
make generate
make manifests
```

---

## 4. Implement the Controller

Write reconciliation logic in:

```
internal/controller/cronjob_controller.go
```

Update manager setup in:

```
cmd/main.go
```

---

## 5. Add Webhooks (Optional)

```bash
kubebuilder create webhook --group batch --version v1 --kind CronJob --defaulting --programmatic-validation
```

Edit webhook logic in:

```
api/v1/cronjob_webhook.go
```

Uncomment webhook and cert-manager sections in:

```
config/default/kustomization.yaml
config/crd/kustomization.yaml
```

Install cert-manager:

```bash
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.18.2/cert-manager.yaml
```

---

## 6. Run Locally

```bash
make install
export ENABLE_WEBHOOKS=false
make run
```

---

## 7. Build and Deploy

```bash
make docker-build docker-push IMG=<some-registry>/<project-name>:tag
```

If using Kind:

```bash
kind load docker-image <some-registry>/<project-name>:tag --name <kind-cluster>
```

Deploy:

```bash
make deploy IMG=<some-registry>/<project-name>:tag
```

---

## 8. Test with a Sample Resource

Create a sample in:

```
config/samples/batch_v1_cronjob.yaml
```

Apply and check:

```bash
kubectl apply -f config/samples/batch_v1_cronjob.yaml
kubectl get cronjobs
kubectl get jobs
```

---

## 9. Run Tests

```bash
make setup-envtest
go test ./internal/controller/ -v
```

---

## Make Commands Reference

| Command              | Description                    |
| -------------------- | ------------------------------ |
| `make generate`      | Update code generated files    |
| `make manifests`     | Update CRDs and RBAC manifests |
| `make install`       | Install CRDs into cluster      |
| `make run`           | Run controller locally         |
| `make docker-build`  | Build Docker image             |
| `make docker-push`   | Push image to registry         |
| `make deploy`        | Deploy controller and CRDs     |
| `make uninstall`     | Remove CRDs from cluster       |
| `make test`          | Run Go unit tests              |
| `make setup-envtest` | Setup binaries for envtest     |

---

## Workflow Summary

1. `kubebuilder init`
2. `kubebuilder create api`
3. Edit `types.go` → `make generate && make manifests`
4. Edit `controller.go` → update `main.go`
5. (Optional) `kubebuilder create webhook` → update webhook files
6. `make install && make run`
7. `make docker-build && make deploy`
8. `kubectl apply -f config/samples/...`
9. `make test`

---


