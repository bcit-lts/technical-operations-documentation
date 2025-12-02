---
tags:
  - nginx
  - monitoring
  - startup
  - liveness
  - readiness
---
# Logging app activity

!!! info

    Logs are captured, processed, and visualized by a [Grafana stack](https://grafana.com/about/grafana-stack/).

Most workloads have an `nginx-unprivileged` frontend that is configured with health endpoints that can be probed by Kubernetes probes and captured by Grafana:

```nginx.conf
...
# Log all non-probe requests (browsers/APIs)
map $is_probe $not_probe { 0 1; 1 0; }

# JSON access log for normal traffic
log_format json escape=json
  '{'
    '"ts":"$time_iso8601",'
    '"remote":"$remote_addr",'
    '"method":"$request_method",'
    '"uri":"$request_uri",'
    '"status":$status,'
    '"bytes":$body_bytes_sent,'
    '"rt":$request_time,'
    '"ref":"$http_referer",'
    '"ua":"$http_user_agent",'
    '"req_id":"$request_id"'
  '}';

# Compact format for probe logs
log_format probe '$remote_addr - $time_local "$request" $status rt=$request_time';
...
# Health endpoints
  location = /healthz {
    access_log off;
    add_header Content-Type application/json;
    return 200 '{"status":"HEALTHY"}';
  }

  location = /healthz/startup {
    access_log off;
    add_header Content-Type application/json;
    return 200 '{"status":"STARTUP_OK"}';
  }

  location = /healthz/ready {
    access_log off;
    add_header Content-Type application/json;
    return 200 '{"status":"READY_OK"}';
  }
...
```
