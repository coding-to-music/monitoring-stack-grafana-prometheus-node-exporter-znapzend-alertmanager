# monitoring-stack-grafana-prometheus-node-exporter-znapzend-alertmanager

# 🚀 Collection of ansible roles to deploy prometheus/alertmanager/grafana monitoring tools 🚀

https://github.com/coding-to-music/monitoring-stack-grafana-prometheus-node-exporter-znapzend-alertmanager

From / By https://github.com/45Drives/monitoring-stack

Example of a Grafana dashboard, using data from Prometheus:

![The exporter container up and running](https://github.com/coding-to-music/terraform-cloudflare-prometheus-grafana/blob/main/images/image2-5.avif?raw=true)

![Grafana screenshot](https://github.com/coding-to-music/terraform-cloudflare-prometheus-grafana/blob/main/images/grafana_prometheus.png?raw=true)

## Digitalocean Droplet Prices

https://github.com/andrewsomething/do-api-slugs

https://slugs.do-api.dev

```
# https://slugs.do-api.dev/

# s-1vcpu-512mb-10gb  $4    10GB
# s-1vcpu-1gb         $6    25GB
# s-1vcpu-2gb         $12   50GB
# s-2vcpu-2gb         $18   60GB
# s-2vcpu-4gb         $24   80GB
# s-4vcpu-8gb         $48   160GB
```

## Environment variables:

```java
variable "do_token" {}    # set via environment value `TF_VAR_do_token`
```

## user interfaces:

- Consul http://localhost:8500
- Fake Service http://localhost:9090/ui
- Prometheus http://localhost:9092/targets
- Node exporter metrics http://localhost:9100/metrics
- Grafana Tempo http://localhost:3000/explore
- Grafana Dashboards http://localhost:3000/dashboards
- Grafana Datasources http://localhost:3000/datasources

## GitHub

```java
git init
git add .
git remote remove origin
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:coding-to-music/monitoring-stack-grafana-prometheus-node-exporter-znapzend-alertmanager.git
git push -u origin main

```

# Monitoring Stack

| Service Name      | Description                                                     |
| ----------------- | --------------------------------------------------------------- |
| Grafana           | Display Statistics & Metrics from database                      |
| Alertmanager      | Query db of metrics and send alerts based on user defined rules |
| Prometheus        | Collect and store metrics scraped from exporters in database    |
| Node Exporter     | Export hardware and OS metrics via http endpoint                |
| ZnapZend Exporter | Export state information on zfs snapshots and replication tasks |

The services outlined above are deployed as containers using either podman or docker depending on Host OS.
Containers are managed via systemd services and/or cockpit-podman module

# Installation

- Clone git repo to "/usr/share"

```sh
cd /usr/share/
git clone https://github.com/45drives/monitoring-stack.git
```

- Included inventory file "hosts" has two groups "metrics" and "exporters"

  - All hosts in the "metrics" group will have prometheus, alertmanager and grafana installed
  - All hosts in the "exporters" group will have node_exporter and znapzend_exporter installed
  - By default "metrics" and "exporters" is populated by localhost. This is sufficient for a single server deployment.
    - To add multiple servers add new hosts in the "exporters" group
    - It is possible to have the metric stack not run on the same server as the exporter services.

- Configure email send/recieve setting for alertmanager in "group_vars/metrics.yml"

- Default ports are defined in the table below, they can be changed in metrics.yml or exporters.yml

| Default Setting          | Value |
| ------------------------ | ----- |
| Prometheus Port          | 9091  |
| Alertmanager Port        | 9093  |
| Grafana Port             | 3000  |
| Grafana Default User     | admin |
| Grafana Default Password | admin |
| Node Exporter Port       | 9100  |
| Znapzend Port            | 9101  |

- Run metrics playbook

```sh
cd /usr/share/monitoring-stack
ansible-playbook -i hosts deploy-monitoring.yml
```

- To uninstall monitoring stack

```sh
ansible-playbook -i hosts purge-monitoring.yml
```

# Verification

To ensure monitoring stack is working as expected, simulate failure condition and you will recieve an email notification
_ Offline a disk in your zpool
_ Set disk as "Offline" in Houston UI, "ZFS + File Sharing"
_ Or in cli: zpool offline tank 1-1
_ After ~30 seconds you should see email with subject line "[FIRING:1] ZpoolDegradedState ($HOSTNAME node warning degraded $POOL_NAME)"
