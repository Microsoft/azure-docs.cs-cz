---
title: Přidání sond stavu do AKS lusků
description: Tento článek poskytuje informace o tom, jak přidat sondy stavu (připravenost a/nebo živý) do AKS do lusků pomocí Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 8c8b8b0090877db7abc8fae0e44f928e8b10dcf5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "84807993"
---
# <a name="add-health-probes-to-your-service"></a>Přidání sond stavu do služby
Adaptér příchozího přenosu dat ve výchozím nastavení zřídí test HTTP GET pro exponované lusky.
Vlastnosti sondy je možné přizpůsobit přidáním [testu připravenosti nebo živého provozu](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) do vaší `deployment` / `pod` specifikace.

## <a name="with-readinessprobe-or-livenessprobe"></a>S `readinessProbe` nebo `livenessProbe`
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

Reference k rozhraní Kubernetes API:
* [Sondy kontejneru](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [Akce HttpGet](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe` a `livenessProbe` jsou podporovány, pokud jsou nakonfigurovány pomocí `httpGet` .
> * Zjišťování na jiném portu, než který je vystavený na straně, se v tuto chvíli nepodporuje.
> * `HttpHeaders`, `InitialDelaySeconds` , `SuccessThreshold` nejsou podporovány.

##  <a name="without-readinessprobe-or-livenessprobe"></a>Bez `readinessProbe` nebo `livenessProbe`
Pokud výše uvedené sondy nejsou k dispozici, pak kontroler příchozího přenosu dat předpokládá, že je služba dostupná `Path` pro zadání `backend-path-prefix` poznámky nebo pro `path` zadání v `ingress` definici služby.

## <a name="default-values-for-health-probe"></a>Výchozí hodnoty pro sondu stavu
Pro jakoukoliv vlastnost, kterou nelze odvodit pomocí testu připravenosti/živých, jsou nastaveny výchozí hodnoty.

| Vlastnost Application Gateway PROBE | Výchozí hodnota |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |