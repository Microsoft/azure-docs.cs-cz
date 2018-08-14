---
title: Kurz Kubernetes v Azure – Škálování aplikace
description: Kurz AKS – Škálování aplikace
services: container-service
author: dlepow
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 61654ae972965800909544554cc93dae511e1ff1
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480268"
---
# <a name="tutorial-scale-application-in-azure-kubernetes-service-aks"></a>Kurz: Škálování aplikace ve službě Azure Kubernetes Service (AKS)

Pokud jste postupovali podle kurzů, máte funkční cluster Kubernetes ve službě AKS a nasadili jste aplikaci Azure Vote.

V tomto kurzu, který je pátou částí sedmidílné série, budete škálovat pody v této aplikaci a vyzkoušíte si jejich automatické škálování. Naučíte se také, jak škálovat počet uzlů virtuálního počítače Azure, aby se změnila kapacita clusteru pro hostování úloh. Mezi dokončené úlohy patří:

> [!div class="checklist"]
> * Škálování uzlů Azure Kubernetes
> * Ruční škálování podů Kubernetes
> * Konfigurace automatického škálování podů, ve kterých běží front-end aplikace

V následujících kurzech se aplikace Azure Vote aktualizuje na novou verzi.

## <a name="before-you-begin"></a>Než začnete

V předchozích kurzech se aplikace zabalila do image kontejneru, tato image se odeslala do Azure Container Registry a vytvořil se cluster Kubernetes. Aplikace se potom spustila v tomto clusteru Kubernetes.

Pokud jste tyto kroky neprovedli a chcete si je projít, vraťte se ke [kurzu 1 – Vytváření imagí kontejneru][aks-tutorial-prepare-app].

## <a name="manually-scale-pods"></a>Ruční škálování podů

Doposud máme nasazený front-end Azure Vote a instanci Redis, oboje s jedinou replikou. K ověření spusťte příkaz [kubectl get][kubectl-get].

```azurecli
kubectl get pods
```

Výstup:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Ručně změňte počet podů v nasazení `azure-vote-front` pomocí příkazu [kubectl scale][kubectl-scale]. Tento příklad zvýší jejich počet na 5.

```azurecli
kubectl scale --replicas=5 deployment/azure-vote-front
```

Spuštěním příkazu [kubectl get pods][kubectl-get] ověřte, že Kubernetes vytváří tyto pody. Asi za minutu budou další pody spuštěné:

```azurecli
kubectl get pods
```

Výstup:

```
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Automatické škálování podů

Kubernetes podporuje [horizontální automatické škálování podů][kubernetes-hpa] pro úpravu počtu podů v nasazení v závislosti na využití procesoru nebo jiné vybrané metrice. [Server metrik][metrics-server] se používá pro poskytování využití prostředků v Kubernetes. Pokud chcete nainstalovat server metrik, naklonujte úložiště GitHub `metrics-server` a nainstalujte vzorové definice prostředků. Obsah těchto definicí YAML si můžete zobrazit na stránce se [serverem metrik pro Kuberenetes 1.8+][metrics-server-github].

```console
git clone https://github.com/kubernetes-incubator/metrics-server.git
kubectl create -f metrics-server/deploy/1.8+/
```

Aby bylo možné použít modul automatického škálování, pody musí mít definovaná omezení a požadavky na procesor. V nasazení `azure-vote-front` kontejner front-endu vyžaduje 0,25 CPU s limitem 0,5 CPU. Nastavení vypadá takto:

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

Následující příklad využívá příkaz [kubectl autoscale][kubectl-autoscale] k automatickému škálování počtu podů v nasazení `azure-vote-front`. Pokud tady využití procesoru přesáhne 50 %, modul automatického škálování zvýší počet podů na maximálně 10.

```azurecli
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Spuštěním následujícího příkazu zobrazíte stav modulu automatického škálování:

```azurecli
kubectl get hpa
```

Výstup:

```
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Po několika minutách se díky minimálnímu zatížení aplikace Azure Vote počet replik podů automaticky sníží na 3.

## <a name="manually-scale-aks-nodes"></a>Ruční škálování uzlů AKS

Pokud jste cluster Kubernetes vytvořili pomocí příkazů v předchozím kurzu, má jeden uzel. Pokud ve vašem clusteru plánujete více nebo méně úloh kontejneru, můžete počet uzlů upravit ručně.

Následující příklad zvýší počet uzlů v clusteru Kubernetes s názvem *myAKSCluster* na tři. Dokončení tohoto příkazu trvá několik minut.

```azurecli
az aks scale --resource-group=myResourceGroup --name=myAKSCluster --node-count 3
```

Výstup je podobný tomuto:

```
"agentPoolProfiles": [
  {
    "count": 3,
    "dnsPrefix": null,
    "fqdn": null,
    "name": "myAKSCluster",
    "osDiskSizeGb": null,
    "osType": "Linux",
    "ports": null,
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_D2_v2",
    "vnetSubnetId": null
  }
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste v clusteru Kubernetes využili různé funkce škálování. Mezi probírané úlohy patří:

> [!div class="checklist"]
> * Ruční škálování podů Kubernetes
> * Konfigurace automatického škálování podů, ve kterých běží front-end aplikace
> * Škálování uzlů Azure Kubernetes

Přejděte k dalšímu kurzu, kde se seznámíte s aktualizací aplikace v Kubernetes.

> [!div class="nextstepaction"]
> [Aktualizace aplikace v Kubernetes][aks-tutorial-update-app]

<!-- LINKS - external -->
[kubectl-autoscale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-scale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale
[kubernetes-hpa]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
[metrics-server-github]: https://github.com/kubernetes-incubator/metrics-server/tree/master/deploy/1.8%2B
[metrics-server]: https://kubernetes.io/docs/tasks/debug-application-cluster/core-metrics-pipeline/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
