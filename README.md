# simple-otel-collector
Get a simple OTel Collector up and running quickly in K8s for testing purposes

## Pre-requisites

1. [Telemetrygen](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/cmd/telemetrygen) (if you want to send fake telemetry)


## Store your License Key
```
kubectl create secret generic newrelic-license --from-literal=license-key=XXXXXXXXNRAL -n simple
```

## Install the OTel collector

Clone this repo.
```
git clone https://github.com/bpschmitt/simple-otel-collector && cd simple-otel-collector
```

Install via Helm.
```
helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
helm upgrade --install simple-otel-collector open-telemetry/opentelemetry-collector -f ./deployment/values.yaml --set mode=deployment --set image.repository="otel/opentelemetry-collector-k8s" --set command.name="otelcol-k8s" -n simple --create-namespace
```

## Generate Telemetry

Set up port forwarding.
```
kubectl port-forward svc/simple-otel-collector-dep 4317 -n simple
```

In a new terminal window:

```
telemetrygen traces --otlp-insecure --otlp-endpoint "localhost:4317"
while true; do telemetrygen traces --otlp-insecure --otlp-endpoint "localhost:8888"; sleep 5; done
```

