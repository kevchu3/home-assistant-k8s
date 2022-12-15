# Home Assistant for Kubernetes

## Kubernetes Installation 

Customize Home Assistant [configuration]:
```
$ vi configuration.yaml
$ base64 -w 0 configuration.yaml
```

Replace the configuration data in [home-assistant-k8s.yaml] Secret with the base64 output:
```
apiVersion: v1
kind: Secret
metadata:
  name: ha-config
  namespace: home-assistant
type: Opaque
data:
  configuration.yaml: IyBodHRwczovL3d3dy5ob21lLWFzc2lzdGFudC5pby9kb2NzL2NvbmZpZ3VyYXRpb24vCmRlZmF1bHRfY29uZmlnOgoKaHR0cDoKICB1c2VfeF9mb3J3YXJkZWRfZm9yOiB0cnVlCiAgdHJ1c3RlZF9wcm94aWVzOgogICAgLSAxMC4wLjAuMC84CiAgICAtIDE3Mi4xNi4wLjAvMTIKICAgIC0gMTkyLjE2OC4wLjAvMTYK    ### <---
```

Replace the ingress host with your resolvable hostname:
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ha-ingress
  namespace: home-assistant
spec:
  rules:
  - host: home-assistant.example.com   ### <---
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: home-assistant
            port:
              number: 80
```

Create Kubernetes resources:
```
$ kubectl apply -f home-assistant-k8s.yaml
```

## Red Hat OpenShift Installation

Instead of using [home-assistant-k8s.yaml], use [home-assistant-ocp.yaml].  Create an imagestream that tracks upstream image updates, along with the OpenShift resources:
```
$ oc import-image home-assistant --from=ghcr.io/home-assistant/home-assistant:latest --scheduled=true --confirm
$ oc apply -f home-assistant-ocp.yaml
```

## License
GPLv3

[configuration]: https://www.home-assistant.io/docs/configuration/
[home-assistant-k8s.yaml]: home-assistant-k8s.yaml
[home-assistant-ocp.yaml]: home-assistant-ocp.yaml
