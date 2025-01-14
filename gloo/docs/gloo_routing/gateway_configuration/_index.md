---
title: Gateway Configuration
weight: 30
description: Gloo allows you to configure properties of your listeners with the Gateway resource and accompnaying plugins. These guides show you how to apply these advanced listener configurations to refine your gateways' behavior.
---


**Gateway** definitions set up the protocols and ports on which Gloo listens for traffic.  For example, by default Gloo will have a gateway configured for HTTTP and HTTPS traffic. Gloo allows you to configure properties of your gateways with several plugins.


These guides show you how to apply these advanced listener configurations to refine your gateways' behavior.

## Overview

For demonstration purposes, let's edit the default gateways that are installed with `glooctl install gateway`.
You can list and edit gateways with `kubectl`.

```bash
kubectl get gateway --all-namespaces
NAMESPACE     NAME          AGE
gloo-system   gateway       2d
gloo-system   gateway-ssl   2d
```

`kubectl edit gateway -n gloo-system gateway`

### Plugin summary

The listener plugin portion of the gateway crd is shown below.

{{< highlight yaml "hl_lines=7-11" >}}
apiVersion: gateway.solo.io/v1
kind: Gateway
metadata: # collapsed for brevity
spec:
  bindAddress: '::'
  bindPort: 8080
  plugins:
    grpcWeb:
      disable: true
    httpConnectionManagerSettings:
      via: reference-string
  useProxyProto: false
status: # collapsed for brevity
{{< /highlight >}}


### Verify your configuration

To verify that your configuration was accepted, inspect the proxy CRDs. They should have the values you specified. 

```bash
kubectl get proxy  --all-namespaces -o yaml
```

{{< highlight yaml "hl_lines=11-15" >}}
apiVersion: v1
items:
- apiVersion: gloo.solo.io/v1
  kind: Proxy
  metadata: # collapsed for brevity
  spec:
    listeners:
    - bindAddress: '::'
      bindPort: 8080
      httpListener:
        listenerPlugins:
          grpcWeb:
            disable: true
          httpConnectionManagerSettings:
            via: reference-string
        virtualHosts:
        - domains:
          - '*'
          name: gloo-system.merged-*
        - domains:
          - solo.io
          name: gloo-system.myvs3
      name: listener-::-8080
      useProxyProto: false
    - bindAddress: '::'
      bindPort: 8443
      httpListener: {}
      name: listener-::-8443
      useProxyProto: false
  status: # collapsed for brevity
kind: List
metadata: # collapsed for brevity
{{< /highlight >}}

