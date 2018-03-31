---
title: Vytvoření Azure Container Service (AKS) interní zátěže
description: Použijte Vyrovnávání zatížení interní s Azure Container Service (AKS).
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 3/29/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 7b9ecdb5364f7c0f5bb68ce693e53bc2c5327337
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="use-an-internal-load-balancer-with-azure-container-service-aks"></a>Použijte Vyrovnávání zatížení interní s Azure Container Service (AKS)

Vyrovnávání zatížení pro vnitřní zpřístupní Kubernetes služby k aplikacím spuštěným ve stejné virtuální síti jako Kubernetes cluster. Tento dokument údaje vytváření interní nástroj s Azure Container Service (AKS).

## <a name="create-internal-load-balancer"></a>Vytvoření interní zátěže.

Pokud chcete vytvořit interní nástroj, sestavení service manifest s typem služby `LoadBalancer` a `azure-load-balancer-internal` poznámky, jak je vidět v následující ukázce.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Po nasazení Vyrovnávání zatížení Azure se vytvoří a k dispozici ve stejné virtuální síti jako AKS cluster. 

![Obrázek AKS interní vyrovnávání zátěže](media/internal-lb/internal-lb.png)

Při načítání službu podrobnosti, IP adresu v `EXTERNAL-IP` sloupec je IP adresa služby Vyrovnávání zatížení interní. 

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   10s
```

## <a name="specify-an-ip-address"></a>Zadejte IP adresu

Pokud chcete použít konkrétní IP adresu s nástrojem pro vyrovnávání zatížení pro vnitřní, přidejte `loadBalancerIP` vlastnost specifikace nástroje pro vyrovnávání zatížení. Zadaná IP adresa se musí nacházet ve stejné podsíti jako AKS cluster a nesmí být již přiřazen k prostředku.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Při načítání podrobností o služby, IP adresa na `EXTERNAL-IP` zadaná IP adresa musí být stejný. 

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="delete-the-load-balancer"></a>Odstranit nástroj pro vyrovnávání zatížení

Po odstranění všech služeb pomocí nástroje pro vyrovnávání zatížení pro vnitřní, je taky odstranit nástroj pro vyrovnávání zatížení, sám sebe.

## <a name="next-steps"></a>Další postup

Další informace o službách Kubernetes na [Kubernetes služeb dokumentaci][kubernetes-services].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/