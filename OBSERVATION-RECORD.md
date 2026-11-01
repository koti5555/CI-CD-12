# Practical-12 Observation Record

| Test / Observation | Expected evidence | Status / Notes |
|---|---|---|
| Updated image build | GitHub Actions Docker build succeeds | Pending |
| Container health | GET /health returns 200 | Pending |
| Metrics endpoint | GET /metrics contains student_api_* metrics | Pending |
| Model readiness | student_api_model_loaded = 1 | Pending |
| Valid prediction | prediction counter increases | Pending |
| Invalid request | HTTP 400 counter increases; validation log exists | Pending |
| Missing route | HTTP 404 counter increases | Pending |
| GitHub Actions | Build, smoke tests and GHCR push pass | Pending |
| GHCR | Practical-12 image available | Pending |
| Render | Existing image-backed service runs updated image | Pending |
| Public metrics | Render /metrics returns Prometheus text | Pending |
| Prometheus target | student-result-api = UP | Pending |
| PromQL | Requests, predictions, errors and latency queries return data | Pending |
| Grafana | Prometheus data source connected; dashboard panels show data | Pending |
| Alert demonstration | StudentAPIClientErrors reaches pending/firing after repeated 4xx | Pending |
| Logs + metrics | Request behavior visible in Render logs and metrics | Pending |

Do not mark an item complete without capturing the corresponding evidence. Screenshots must not expose credentials or student data.
