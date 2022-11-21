# kubernetes prometheus

Complete prometheus, alertmanager and grafana monitoring stack setup on existing Kubernetes.

You can find the full tutorial from here https://devopscube.com/setup-prometheus-monitoring-on-kubernetes/

Idea of this repo to understand all the components involved in prometheus setup.

## Installation

- Setup `KUBECONFIG` in local so as to enable the auth to k8s cluster

- Setup monitor namespace

  ```
  kubectl create namespace monitor
  ```

- Deploy `prometheus`
    ```
    auto/deploy-prometheus
    ```

- Deploy `kube-state-metrics`
    ```
    auto/deploy-kube-state-metrics
    ```

- Deploy alertmanager
    ```
    auto/deploy-alertmanager
    ```

- Deploy metrics-server
    ```
    auto/destroy-metrics-server
    ```

- Deploy `grafana`
    ```
    auto/deploy-grafana
    ```

- Forward grafana in local(optional)
  
  ```
  kubectl port-forward -n monitor svc/grafana 3000:3000
  ```

- Login grafana(optional)

  http://localhost:3000

  admin/admin

- Add dashboard for grafana

  - Find dashboard resource from official 
    https://grafana.com/grafana/dashboards/315
  - Add the following right behind `prometheus/config-map.yaml`
    ```
    scrape_configs:
    - job_name: kubernetes-nodes-cadvisor
      scrape_interval: 10s
      scrape_timeout: 10s
      scheme: https  # remove if you want to scrape metrics on insecure port
      tls_config:
        ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
      bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
      kubernetes_sd_configs:
        - role: node
      relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        # Only for Kubernetes ^1.7.3.
        # See: https://github.com/prometheus/prometheus/issues/2916
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor
      metric_relabel_configs:
        - action: replace
          source_labels: [id]
          regex: '^/machine\.slice/machine-rkt\\x2d([^\\]+)\\.+/([^/]+)\.service$'
          target_label: rkt_container_name
          replacement: '${2}-${1}'
        - action: replace
          source_labels: [id]
          regex: '^/system\.slice/(.+)\.service$'
          target_label: systemd_service_name
          replacement: '${1}'
    ```

  - Go to http://localhost:3000/dashboard/import
  - Import via dashboard id from above official link
  - Check the outcome http://localhost:3000/dashboards

    More dashboard: https://grafana.com/grafana/dashboards/13105
