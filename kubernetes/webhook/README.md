# Kubernetes Webhook Template

Opinionated template on how to set up a Kubernetes Webhook.
It requires [cert-manager](https://cert-manager.io) version 0.13.0 installed on the cluster.

Example:
```dhall
-- change the imports to be http imports from this repository
let Webhook = ./package.dhall

let k8s = ../k8s/1.15.dhall

let exampleWebhook =
      Webhook::{
      , imageName = "docker/whalesay:latest" -- replace with webhook image
      , name = "whalesay"
      , namespace = "default"
      , path = "/mutate"
      , port = 8080
      , rules =
        [ k8s.RuleWithOperations::{
          , operations = [ "CREATE", "UPDATE" ]
          , apiGroups = [ "" ]
          , apiVersions = [ "v1" ]
          , resources = [ "pods" ]
          }
        ]
      }

in  Webhook.renderMutatingWebhook exampleWebhook
```

To implement a webhook in Haskell, you can use https://github.com/EarnestResearch/kubernetes-webhook-haskell/
