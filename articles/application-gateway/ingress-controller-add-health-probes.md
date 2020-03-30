---
title: Přidání sond stavu do podů AKS
description: Tento článek obsahuje informace o tom, jak přidat sondy stavu (připravenost nebo živost) do podů AKS s aplikační bránou.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5d0543a3a43d53e462a6406312faddf37d2653c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795597"
---
# <a name="add-health-probes-to-your-service"></a>Přidání sond stavu do služby
Ve výchozím nastavení ingress řadič zřídí http get sondu pro exponované pody.
Vlastnosti sondy lze přizpůsobit přidáním [připravenosti nebo sondy připravenosti](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) nebo připravenosti do specifikace. `deployment` / `pod`

## <a name="with-readinessprobe-or-livenessprobe"></a>S `readinessProbe` nebo`livenessProbe`
```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aspnetapp
spec:
  replicas: 3
  template:
    metadata:
      labels:
        service: site
    spec:
      containers:
      - name: aspnetapp
        image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
          periodSeconds: 3
          timeoutSeconds: 1
```

Kubernetes API Reference:
* [Kontejnerové sondy](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [Akce HttpGet](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe`a `livenessProbe` jsou podporovány, pokud jsou konfigurovány s . `httpGet`
> * Zjišťování na jiném portu, než je port vystavený v podu, není aktuálně podporováno.
> * `HttpHeaders`, `InitialDelaySeconds` `SuccessThreshold` , nejsou podporovány.

##  <a name="without-readinessprobe-or-livenessprobe"></a>Bez `readinessProbe` nebo`livenessProbe`
Pokud výše uvedené sondy nejsou k dispozici, pak Ingress Controller `Path` předpokládat, že služba je dosažitelná na určené pro `backend-path-prefix` poznámku `path` nebo zadanou v `ingress` definici služby.

## <a name="default-values-for-health-probe"></a>Výchozí hodnoty pro sondu stavu
Pro všechny vlastnosti, které nelze odvodit prominstovat připravenost/živost sonda Default hodnoty jsou nastaveny.

| Vlastnost probe aplikační brány | Výchozí hodnota |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |