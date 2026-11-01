# MLOps Practical 12 — Prometheus and Grafana Monitoring

## Project continuity
This repository extends Practical-11 without retraining the model. The Dockerfile uses the tested Practical-11 GHCR image as its base, preserving its model artifact and Python dependencies. The actual Practical-11 API is Flask (not FastAPI): port 5000, routes /, /health, /predict. It does not provide /docs or FastAPI's automatic 422 responses. Invalid payloads return 400.

## Metrics
- student_api_http_requests_total — HTTP request counter by method, path and status.
- student_api_http_request_duration_seconds — request latency histogram.
- student_api_predictions_total — successful and failed inference attempts.
- student_api_model_loaded — 1 when the model loads successfully.
- Prometheus up — scrape target health.

The /metrics scrape route is excluded from user-traffic counters and latency. Metric labels contain route/method/status only; no student records or identifiers are logged or exposed.

## Build and test
GitHub Actions builds from the Practical-11 GHCR image, installs prometheus-client, runs the updated container, checks /health, /metrics, a valid prediction, invalid input (400), and a missing route (404), then publishes the tested image to the Practical-12 GHCR package.

Image: ghcr.io/koti5555/student-result-prediction-ci-cd-12:latest

## Render
Update the existing image-backed Render service to the Practical-12 image above. Keep PORT=5000 and health check /health. After deployment, test:
- https://YOUR-RENDER-HOST.onrender.com/health
- https://YOUR-RENDER-HOST.onrender.com/metrics
- POST /predict with the four existing numeric fields.

Do not create a source-build Render service for this image; it is already built and published by GitHub Actions. The Render image-backed service may require Manual Deploy after the GHCR image is updated.

## Local Prometheus and Grafana
Edit prometheus.yml and replace the placeholder hostname with your actual Render hostname (hostname only; no https://). With Docker installed, from this folder:

```powershell
docker network create mlops-monitoring
docker run -d --name prometheus --network mlops-monitoring -p 9090:9090 -v "${PWD}/prometheus.yml:/etc/prometheus/prometheus.yml:ro" -v "${PWD}/student_api_alerts.yml:/etc/prometheus/student_api_alerts.yml:ro" prom/prometheus
docker run -d --name grafana --network mlops-monitoring -p 3000:3000 grafana/grafana
```

Prometheus: http://localhost:9090/targets
Grafana: http://localhost:3000
Grafana Prometheus data source URL: http://prometheus:9090

## PromQL
```promql
student_api_model_loaded
sum(student_api_http_requests_total)
sum(rate(student_api_http_requests_total[1m]))
sum(student_api_predictions_total{result="success"})
sum(rate(student_api_http_requests_total{status=~"4..|5.."}[5m]))
histogram_quantile(0.95, sum by (le) (rate(student_api_http_request_duration_seconds_bucket[5m])))
up{job="student-result-api"}
```

Alert rules evaluate conditions in Prometheus. Email/chat notifications require an additional Alertmanager or Grafana contact-point configuration and are outside this practical's core scope.

## Important limitation
The monitoring stack runs locally and scrapes the public Render endpoint. Docker Desktop/Engine is required for the local Prometheus and Grafana containers. If you do not have Docker installed, you can still complete the application instrumentation and cloud redeployment, but cannot claim the local Prometheus/Grafana evidence until a Docker-capable machine is available.
