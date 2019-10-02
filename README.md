# The Jenkins X Status Badge App

The Status Badge App can be installed by running:

`jx add app jx-app-statusbadge`

# shields.io configuration

Use the custom endpoint configuration https://shields.io/endpoint.  The url for your build will be:

```
https://statusbadge-jx.<your domain>/[<org>/]<repo>
```

e.g.
https://statusbadge-jx.jenkins-x.live/jx

To use this in a markdown badge, use:

```
![Build Status](https://img.shields.io/endpoint?url=https%3A%2F%2Fstatusbadge-jx.jenkins-x.live%2Fjx)
```

To generate a badge like: 

![Build Status](https://img.shields.io/endpoint?url=https%3A%2F%2Fstatusbadge-jx.jenkins-x.live%2Fjx)

# Ingress

Depending on your cluster configuration, you may also need to add an ingress configuration for this app to be exposed externally. e.g. 

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  annotations:
    kubernetes.io/ingress.class: nginx
  name: statusbadge
spec:
  rules:
  - host: statusbadge{{ .Values.cluster.namespaceSubDomain }}{{ .Values.cluster.domain }}
    http:
      paths:
      - backend:
          serviceName: jenkins-x-status-badge
          servicePort: 80
{{- if .Values.cluster.tls }}
  tls:
  - hosts:
    - statusbadge{{ .Values.cluster.namespaceSubDomain }}{{ .Values.cluster.domain }}
{{- if eq .Values.certmanager.production "true" }}
    secretName: "tls-{{ .Values.cluster.domain | replace "." "-" }}-p"
{{- else }}
    secretName: "tls-{{ .Values.cluster.domain | replace "." "-" }}-s"
{{- end }}
{{- end }}
```

# Service Account

By default, the app will use the default kubernetes service account to query pipeline activities, to override this account use:

```
status-badge:
  service:
    serviceAccount: <service account>
```
 
e.g.

```
status-badge:
  service:
    serviceAccount: tekton-bot
```
