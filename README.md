# Home Assistant for Kubernetes

## Kubernetes Installation 

Customize Home Assistant [configuration]:
```
$ vi configuration.yaml
$ base64 -w 0 configuration.yaml
```

Using the resource definitions provided in [kubernetes/home-assistant.yaml], replace the configuration data in the Secret with the base64 output:
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
$ kubectl apply -f kubernetes/home-assistant.yaml
```

## Red Hat OpenShift Installation

The application installation on Red Hat OpenShift leverages the use of imagestreams and ImageChange triggers on the DeploymentConfig to automatically update whenever a new image is available.

Instead of using the resource definitions in `kubernetes/home-assistant.yaml`, use [openshift/home-assistant.yaml].  Create an imagestream that tracks upstream image updates, along with the OpenShift resources:
```
$ oc import-image home-assistant --from=ghcr.io/home-assistant/home-assistant:latest --scheduled=true --confirm
$ oc apply -f openshift/home-assistant.yaml
```

## License
GPLv3

[configuration]: https://www.home-assistant.io/docs/configuration/
[kubernetes/home-assistant.yaml]: kubernetes/home-assistant.yaml
[openshift/home-assistant.yaml]: openshift/home-assistant.yaml
