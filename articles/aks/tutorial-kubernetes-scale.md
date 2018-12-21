---
title: Kurz Kubernetes v Azure – Škálování aplikace
description: V tomto kurzu Azure Kubernetes Service (AKS) zjistíte, jak škálovat uzly a pody v Kubernetes a jak implementovat automatické horizontální škálování podů.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 8d07c87a1849a25738c433b7a4c2753b51661947
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53722715"
---
# <a name="tutorial-scale-applications-in-azure-kubernetes-service-aks"></a>Kurz: Škálování aplikací ve službě Azure Kubernetes Service (AKS)

Pokud jste postupovali podle kurzů, máte funkční Kubernetes cluster ve službě AKS a nasadili ukázkovou aplikaci Azure Vote. V tomto kurzu, který je pátou částí sedmidílné série, budete škálovat pody v této aplikaci a vyzkoušíte si jejich automatické škálování. Naučíte se také, jak škálovat počet uzlů virtuálního počítače Azure, aby se změnila kapacita clusteru pro hostování úloh. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Škálování uzlů Kubernetes
> * Ruční škálování podů Kubernetes, ve kterých se spouští vaše aplikace
> * Konfigurace automatického škálování podů, ve kterých se spouští front-end aplikace

V dalších kurzech se aktualizuje aplikace Azure Vote na novou verzi.

## <a name="before-you-begin"></a>Před zahájením

V předchozích kurzech se aplikace zabalila do image kontejneru. Tato image se odeslala do Azure Container Registry a vytvoření clusteru AKS. Aplikace se pak nasadí do clusteru AKS. Pokud jste tyto kroky neprovedli a chcete postupovat s námi, začněte tématem [kurzu 1 – vytváření imagí kontejneru][aks-tutorial-prepare-app].

Tento kurz vyžaduje, že používáte Azure CLI verze 2.0.53 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="manually-scale-pods"></a>Ruční škálování podů

Při nasazení front-endu Azure Vote a instance Redis v předchozích kurzech se vytvořila jedna replika. Pokud chcete zobrazit počet a stav podů ve vašem clusteru, použijte příkaz [kubectl get][kubectl-get] následujícím způsobem:

```console
kubectl get pods
```

Následující příklad výstupu ukazuje jeden pod front-endu a jeden pod back-endu:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Pokud chcete ručně změnit počet podů v nasazení *azure-vote-front*, použijte příkaz [kubectl scale][kubectl-scale]. Následující příklad zvýší počet podů front-endu na *5*:

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

Spustit [kubectl get pods] [ kubectl-get] znovu k ověření, že AKS vytvoří budou další pody. Asi za minutu budou další pody dostupné ve vašem clusteru:

```console
$ kubectl get pods

                                    READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Automatické škálování podů

Kubernetes podporuje [horizontální automatické škálování podů][kubernetes-hpa] pro úpravu počtu podů v nasazení v závislosti na využití procesoru nebo jiné vybrané metrice. K poskytování využití prostředků v Kubernetes se používá [server metrik][metrics-server], který je automaticky nasazený v clusterech AKS verze 1.10 nebo novější. Pokud chcete zobrazit verzi clusteru AKS, použijte příkaz [az aks show][az-aks-show], jak je znázorněno v následujícím příkladu:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query kubernetesVersion
```

Pokud je verze vašeho clusteru AKS nižší než *1.10*, nainstalujte server metrik, jinak tento krok přeskočte. Pokud chcete nainstalovat, naklonujte `metrics-server` úložiště GitHub a nainstalovat definice prostředků příklad. Obsah těchto definicí YAML si můžete zobrazit na stránce se [serverem metrik pro Kuberenetes 1.8+][metrics-server-github].

```console
git clone https://github.com/kubernetes-incubator/metrics-server.git
kubectl create -f metrics-server/deploy/1.8+/
```

Aby bylo možné použít modul automatického škálování, pody musí mít definovaná omezení a požadavky na procesor. V `azure-vote-front` nasazení, kontejner front-endu již 0,25 CPU s limitem 0,5 CPU. Těchto omezení a požadavky na zdroje jsou definovány, jak je znázorněno v následujícím příklad fragmentu kódu:

```yaml
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

Následující příklad využívá příkaz [kubectl autoscale][kubectl-autoscale] k automatickému škálování počtu podů v nasazení *azure-vote-front*. Pokud využití procesoru přesáhne 50 %, automatického škálování zvýší počet podů až do maximálního počtu *10* instancí. Minimálně *3* instancí je pak definované pro nasazení:

```console
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Pokud chcete zobrazit stav modulu automatického škálování, použijte příkaz `kubectl get hpa` následujícím způsobem:

```
$ kubectl get hpa

NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Po několika minutách se díky minimálnímu zatížení aplikace Azure Vote počet replik podů automaticky sníží na 3. Opětovným použitím příkazu `kubectl get pods` můžete zobrazit odebírání nepotřebných podů.

## <a name="manually-scale-aks-nodes"></a>Ruční škálování uzlů AKS

Pokud jste cluster Kubernetes vytvořili pomocí příkazů v předchozím kurzu, má jeden uzel. Pokud ve vašem clusteru plánujete více nebo méně úloh kontejneru, můžete počet uzlů upravit ručně.

Následující příklad zvýší počet uzlů v clusteru Kubernetes s názvem *myAKSCluster* na tři. Dokončení tohoto příkazu trvá několik minut.

```azurecli
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 3
```

Když má byla úspěšně změněna velikost clusteru, výstup je podobný následujícím příkladu:

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

## <a name="next-steps"></a>Další postup

V tomto kurzu jste v clusteru Kubernetes využili různé funkce škálování. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Ruční škálování podů Kubernetes, ve kterých se spouští vaše aplikace
> * Konfigurace automatického škálování podů, ve kterých se spouští front-end aplikace
> * Ruční škálování uzlů Kubernetes

V dalším kurzu se dozvíte, jak aktualizovat aplikaci v Kubernetes.

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
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
