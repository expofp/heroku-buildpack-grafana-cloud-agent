# Heroku Buildpack for Grafana Cloud Agent

This is an unofficial Heroku buildpack for
[Grafana Cloud Agent](https://github.com/grafana/agent) deployments.

## Usage

### Binary
Automatically [downloads](https://github.com/grafana/agent/releases/) binary defined with `GRAFANA_AGENT_VERSION` env variable that will be placed as `bin/grafana-agent`.

Example:
```text
GRAFANA_AGENT_VERSION=v0.31.0-rc.0
```

### Config
Your `Procfile` and config file  `config/grafana-agent.yml` should be placed into the application git repository root.

The buildpack will [substitute any environment variables](https://grafana.com/docs/agent/latest/configuration/). 

Example:

```yaml
---
server:
  log_level: debug
      
metrics:
  global:
    scrape_interval: 60s
    remote_write:
      - url: https://prometheus-us-central1.grafana.net/api/prom/push
        basic_auth:
          username: "${GRAFANA_CLOUD_USERNAME}"
          password: "${GRAFANA_CLOUD_API_KEY}"
  configs:
    - name: agent
      scrape_configs:
        - job_name: grafana-agent
          static_configs:
            - targets: ["127.0.0.1:${PORT}"]
              labels:
                env: 'prod'
          relabel_configs:
            - source_labels: ["__address__"]
              regex: ".*"
              target_label: instance
              replacement: 'grafana-agent.herokuapp.com'
    
```

