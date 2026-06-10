# M5-B1 + M5-B2 — Pyrenex Prod (architecture, CI/CD, monitoring, éval continue)

> **Repo template GitHub.** Un·e des 2 du binôme clique **« Use this
> template »** → `M5-B1-pyrenex-prod-<binome>`, puis ajoute l'autre comme
> collaborateur. Vous partez du **scoring v2** (modèle M1 fourni) et vous le
> mettez en **production complète** : 3 services orchestrés, CI/CD, monitoring
> Grafana, runbook, puis (B2) évaluation continue + tracking MLflow.

---

## 🚀 Démarrage (le service `model` tourne déjà)

```bash
# 1. Environnement de tests local (optionnel mais conseillé)
python -m venv .venv && source .venv/bin/activate
pip install -r requirements-dev.txt

# 2. Vérifier que la base fournie passe les tests
pytest -v services/model/tests

# 3. Lancer ce qui est déjà câblé (model + prometheus + grafana)
docker compose up --build
```

> ⚠️ **Ports hôte** : frontend **8088** (pas 8080), Grafana **3001** (pas 3000)
> — pour éviter les conflits courants. Model 8000, backend 8001, Prometheus 9090.

Au départ, seuls `model`, `prometheus` et `grafana` démarrent : à vous
d'ajouter `backend` + `frontend` et de compléter le reste (cf. TODO).

---

## 🧭 Ce que vous construisez (et où est l'aide)

| # | À faire | Fichier | Mini-cours |
|---|---|---|---|
| 1 | Service `backend` orchestrateur (`/score`, `/metrics`) | `services/backend/app/main.py` | `02` |
| 2 | Service `frontend` (formulaire + fetch `/api/score`) | `services/frontend/html/index.html` | — |
| 3 | Compléter `docker-compose.yml` (backend + frontend) | `docker-compose.yml` | `01` |
| 4 | Dashboard Grafana custom (3 panels vie/vitesse/qualité) | `grafana/provisioning/dashboards/` | `04` |
| 5 | Pipeline CI/CD (test → build → push GHCR + tag) | `.github/workflows/ci.yml` | `03` |
| 6 | Runbook d'astreinte (4 procédures) | `runbook.md` | `05` |
| **B2** | Évaluation continue + **MLflow** + étape CI bloquante | `scripts/evaluate_model_TEMPLATE.py`, `evaluation_thresholds_TEMPLATE.md` | `03`, `04`, `05`(B2) |

> Le service `model` (déjà fourni) est votre **exemple de référence** : il
> expose déjà `/metrics` — répliquez ce pattern sur le `backend`.

---

## 📁 Structure

```
services/
  model/        # FOURNI — API scoring M1-B2 + /metrics (ne pas réécrire)
  backend/      # À COMPLÉTER — orchestrateur
  frontend/     # À COMPLÉTER — formulaire nginx
prometheus/     # FOURNI — scrape config
grafana/provisioning/
  datasources/  # FOURNI — datasource Prometheus
  dashboards/   # provider fourni ; le dashboard JSON = à vous (TODO 4)
.github/workflows/ci.yml   # squelette (job test fourni)
runbook.md                 # template 4 sections
scripts/evaluate_model_TEMPLATE.py   # B2 — MLflow pré-câblé
data/reference_set_TEMPLATE.csv      # B2 — exemple à remplacer
evaluation_thresholds_TEMPLATE.md    # B2 — seuils à justifier
ressources/                # 📚 mini-cours d'appui (lecture juste-à-temps)
```

---

## 📚 Ressources

Voir [`./ressources/`](./ressources/) — 7 mini-cours + `liens_officiels.md`.
Lecture **juste-à-temps** : ouvrez le mini-cours de la tâche en cours.

---

## ✅ Critères de réussite (rappel)

- `docker compose up --build` démarre les 3 services < 60 s, healthchecks verts.
- `/metrics` exposé côté model **et** backend ; dashboard Grafana provisionné auto.
- Workflow CI **vert** ; le **contract test** du modèle bloque la release si rouge.
- (B2) `evaluate_model.py` trace ≥ 2 runs MLflow comparables ; une dégradation
  volontaire (`--degrade`) fait **échouer la release** (exit ≠ 0).
- Runbook : 4 procédures lisibles par un·e SRE non data.
- Commits binôme : `Co-authored-by:` ou auteurs nominatifs.

## 🆘 Bloqué·e·s ?

1. Relisez le mini-cours de la tâche en cours (`ressources/`).
2. Le service `model` est votre exemple qui marche : copiez ses patterns.
3. 30 min sur un bloquant → Discord `fil-M5`.
