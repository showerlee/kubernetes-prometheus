# kubernetes prometheus

Complete prometheus and grafana monitoring stack setup on existing Kubernetes.

You can find the full tutorial from here https://devopscube.com/setup-prometheus-monitoring-on-kubernetes/

Idea of this repo to understand all the components involved in prometheus setup.

## Installation

- Setup `KUBECONFIG` in local so as to enable the auth to k8s cluster

- Setup monitor namespace

  ```
  kubectl create namespace monitor
  ```

- Run `auto/deploy-prometheus` to deploy prometheus

- Run `auto/deploy-kube-state-metrics` to deploy kube-state-metrics

- Run `auto/deploy-grafana` to deploy grafana


# Other Manifest repos

Alert manager Manifests: https://github.com/bibinwilson/kubernetes-alert-manager

Node Exporter manifests: https://github.com/bibinwilson/kubernetes-node-exporter


