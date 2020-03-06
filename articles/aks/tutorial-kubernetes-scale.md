---
title: Kurz Kubernetes v Azure – Škálování aplikace
description: V tomto kurzu Azure Kubernetes Service (AKS) zjistíte, jak škálovat uzly a pody v Kubernetes a jak implementovat automatické horizontální škálování podů.
services: container-service
ms.topic: tutorial
ms.date: 01/14/2019
ms.custom: mvc
ms.openlocfilehash: 7db80e9bf0bd864762a88680132d77a3c5d21f19
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77621098"
---
# <a name="tutorial-scale-applications-in-azure-kubernetes-service-aks"></a>Kurz: Škálování aplikací ve službě Azure Kubernetes Service (AKS)

Pokud jste postupovali podle kurzů, máte funkční cluster Kubernetes v AKS a nasadili jste ukázkovou hlasovací aplikaci Azure. V tomto kurzu, který je pátou částí sedmidílné série, budete škálovat pody v této aplikaci na více instancí a vyzkoušíte si jejich automatické škálování. Naučíte se také, jak škálovat počet uzlů virtuálního počítače Azure, aby se změnila kapacita clusteru pro hostování úloh. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Škálování uzlů Kubernetes
> * Ruční škálování podů Kubernetes, ve kterých se spouští vaše aplikace
> * Konfigurace automatického škálování podů, ve kterých se spouští front-end aplikace

V dalších kurzech se hlasová aplikace Azure aktualizuje na novou verzi.

## <a name="before-you-begin"></a>Než začnete

V předchozích kurzech byla aplikace zabalena do image kontejneru. Tato image se nahrála do Azure Container Registry a vytvořili jste cluster AKS. Aplikace se pak nasadí do clusteru AKS. Pokud jste tyto kroky neudělali a chcete je sledovat, začněte s [kurzem 1 – vytváření imagí kontejneru][aks-tutorial-prepare-app].

Tento kurz vyžaduje, abyste spustili Azure CLI verze 2.0.53 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="manually-scale-pods"></a>Ruční škálování podů

Při nasazení front-endu Azure Vote a instance Redis v předchozích kurzech se vytvořila jedna replika. Chcete-li zobrazit počet a stav lusků ve vašem clusteru, použijte příkaz [kubectl Get][kubectl-get] následujícím způsobem:

```console
kubectl get pods
```

Následující příklad výstupu ukazuje jeden pod front-endu a jeden pod back-endu:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Pokud chcete ručně změnit počet lusků v nasazení *Azure – hlasování* , použijte příkaz [kubectl Scale][kubectl-scale] . Následující příklad zvýší počet podů front-endu na *5*:

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

Spusťte [kubectl][kubectl-get] a znovu se zajistěte, aby AKS vytvořil další lusky. Asi za minutu budou další pody dostupné ve vašem clusteru:

```console
kubectl get pods

                                    READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Automatické škálování podů

Kubernetes podporuje [horizontální automatické škálování podů][kubernetes-hpa] pro úpravu počtu podů v nasazení v závislosti na využití procesoru nebo jiné vybrané metrice. [Server metriky][metrics-server] slouží k poskytování využití prostředků Kubernetes a je automaticky nasazen v clusterech AKS verze 1,10 a vyšší. Pokud chcete zobrazit verzi clusteru AKS, použijte příkaz [AZ AKS show][az-aks-show] , jak je znázorněno v následujícím příkladu:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query kubernetesVersion --output table
```

> [!NOTE]
> Pokud je váš cluster AKS menší než *1,10*, server metriky se neinstaluje automaticky. Pokud chcete nainstalovat, naklonujte `metrics-server` úložiště GitHub a nainstalujte ukázkové definice prostředků. Pokud si chcete zobrazit obsah těchto definicí YAML, přečtěte si téma [Server metrik pro Kuberenetes 1.8 +][metrics-server-github].
> 
> ```console
> git clone https://github.com/kubernetes-incubator/metrics-server.git
> kubectl create -f metrics-server/deploy/1.8+/
> ```

Chcete-li použít automatické škálování, všechny kontejnery v luskech a vaše lusky musí mít definované požadavky na procesor a omezení. V nasazení `azure-vote-front` kontejner front-endu již vyžádal 0,25 procesor, a to s limitem 0,5 CPU. Tyto požadavky na prostředky a omezení jsou definovány tak, jak je znázorněno v následujícím ukázkovém kódu:

```yaml
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

Následující příklad používá příkaz [kubectl AutoScale][kubectl-autoscale] k automatickému škálování počtu lusků v nasazení v *Azure – hlasování* . Pokud průměrné využití procesoru napříč všemi lusky překročí 50% jejich vyžádaného využití, přizpůsobuje se automatické škálování až do maximálního počtu *10* instancí. Pro nasazení je pak definováno minimálně *3* instance:

```console
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Případně můžete vytvořit soubor manifestu, který definuje chování automatického škálování a omezení prostředků. Následuje příklad souboru manifestu s názvem `azure-vote-hpa.yaml`.

```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: azure-vote-back-hpa
spec:
  maxReplicas: 10 # define max replica count
  minReplicas: 3  # define min replica count
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: azure-vote-back
  targetCPUUtilizationPercentage: 50 # target CPU utilization


apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: azure-vote-front-hpa
spec:
  maxReplicas: 10 # define max replica count
  minReplicas: 3  # define min replica count
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: azure-vote-front
  targetCPUUtilizationPercentage: 50 # target CPU utilization
```

Použijte `kubectl apply` pro použití automatického škálování definovaného v souboru manifestu `azure-vote-hpa.yaml`.

```
kubectl apply -f azure-vote-hpa.yaml
```

Pokud chcete zobrazit stav modulu automatického škálování, použijte příkaz `kubectl get hpa` následujícím způsobem:

```
kubectl get hpa

NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Po několika minutách se díky minimálnímu zatížení aplikace Azure Vote počet replik podů automaticky sníží na 3. Opětovným použitím příkazu `kubectl get pods` můžete zobrazit odebírání nepotřebných podů.

## <a name="manually-scale-aks-nodes"></a>Ruční škálování uzlů AKS

Pokud jste cluster Kubernetes vytvořili pomocí příkazů v předchozím kurzu, má dva uzly. Pokud ve vašem clusteru plánujete více nebo méně úloh kontejneru, můžete počet uzlů upravit ručně.

Následující příklad zvýší počet uzlů v clusteru Kubernetes s názvem *myAKSCluster* na tři. Dokončení tohoto příkazu trvá několik minut.

```azurecli
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 3
```

Po úspěšném škálování clusteru se výstup podobá následujícímu příkladu:

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
[metrics-server]: https://v1-13.docs.kubernetes.io/docs/tasks/debug-application-cluster/core-metrics-pipeline/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
