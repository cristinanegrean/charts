# Stackdriver Exporter

Prometheus exporter for Stackdriver, allowing for Google Cloud metrics.  You
must have appropriate IAM permissions for this exporter to work.  If you
are passing in an IAM key then you must have:

* monitoring.metricDescriptors.list
* monitoring.timeSeries.list

These are contained within `roles/monitoring.viewer`.  If you're using legacy
access scopes, then you must have
`https://www.googleapis.com/auth/monitoring.read`.

Learn more: https://github.com/frodenas/stackdriver_exporter

## TL;DR;

```bash
$ helm install stable/stackdriver-exporter --set stackdriver.projectId=google-project-name
```

## Introduction

This chart creates a Stackdriver-Exporter deployment on a
[Kubernetes](http://kubernetes.io) cluster using the [Helm](https://helm.sh)
package manager.

## Prerequisites

- Kubernetes 1.8+ with Beta APIs enabled

## Installing the Chart

To install the chart with the release name `my-release`:

```bash
$ helm install --name my-release stable/stackdriver-exporter --set stackdriver.projectId=google-project-name
```

The command deploys Stackdriver-Exporter on the Kubernetes cluster using the default configuration. 
The [configuration](#configuration) section lists the parameters that can be configured during installation.

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```bash
$ helm delete --purge my-release
```
The command removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration

The following table lists the configurable parameters of the Stackdriver-Exporter chart and their default values.

Parameter                                                   | Description                                                                     | Default
----------------------------------------------------------- | ------------------------------------------------------------------------------- | --------------------------------
`replicaCount`                                              | Desired number of pods                                                          | `1`
`restartPolicy`                                             | Container restart policy                                                        | `Always`
`image.repository`                                          | Container image repository                                                      | `frodenas/stackdriver-exporter`
`image.tag`                                                 | Container image tag                                                             | `v0.6.0`
`image.pullPolicy`                                          | Container image pull policy                                                     | `IfNotPresent`
`service.type`                                              | Type of service to create                                                       | `ClusterIP`
`service.httpPort`                                          | Port for the http service                                                       | `9255`
`service.annotations`                                       | Deployment annotations                                                          | `{}`
`env.STACKDRIVER_EXPORTER_GOOGLE_PROJECT_ID`                | GCP Project ID                                                                  | ``
`env.STACKDRIVER_EXPORTER_MAX_RETRIES`                      | Max number of retries that should be attempted on errors from Stackdriver       | `0`
`env.STACKDRIVER_EXPORTER_HTTP_TIMEOUT`                     | How long should Stackdriver_Exporter wait for a result from the Stackdriver API | `10s`
`env.STACKDRIVER_EXPORTER_MAX_BACKOFF_DURATION`             | Max time between each request in an exponential backoff scenario                | `5s`
`env.STACKDRIVER_EXPORTER_BACKODFF_JITTER_BASE`             | The amount of jitter to introduce in an exponential backoff scenario            | `1s`
`env.STACKDRIVER_EXPORTER_RETRY_STATUSES`                   | The HTTP statuses that should trigger a retry                                   | `503`
`env.STACKDRIVER_EXPORTER_MONITORING_METRICS_TYPE_PREFIXES` | Comma separated Metric Type prefixes                                            | `compute.googleapis.com/instance/cpu`
`env.STACKDRIVER_EXPORTER_MONITORING_METRICS_INTERVAL`      | Metrics interval to request from GCP                                            | `5m`
`env.STACKDRIVER_EXPORTER_MONITORING_METRICS_OFFSET`        | Offset (into the past) to request                                               | `0s`
`env.STACKDRIVER_EXPORTER_WEB_LISTEN_ADDRESS`               | Port to listen on                                                               | `:9255`
`env.STACKDRIVER_EXPORTER_WEB_TELEMETRY_PATH`               | Path under which to expose metrics                                              | `/metrics`

Specify each parameter using the `--set key=value[,key=value]` argument to
`helm install`. For example,


```bash
$ helm install --name my-release \
    --set key_1=value_1,key_2=value_2 \
    stable/stackdriver-exporter
```

Alternatively, a YAML file that specifies the values for the parameters can be
provided while installing the chart. For example,

```bash
# example for staging
$ helm install --name my-release -f values.yaml stable/stackdriver-exporter
```

> **Tip**: You can use the default [values.yaml](values.yaml), as long as you provide a value for env.STACKDRIVER_EXPORTER_GOOGLE_PROJECT_ID

## Google Storage Metrics

In order to get metrics for GCS you need to ensure the metrics interval is >
24h.  You can read more information about this in [this bug
report](https://github.com/frodenas/stackdriver_exporter/issues/14).

The easiest way to do this is to create two separate exporters with different
prefixes and intervals, to ensure you gather all appropriate metrics.
