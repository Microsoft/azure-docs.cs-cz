---
title: Kurz Kubernetes v Azure – Škálování aplikace
description: V tomto kurzu Azure Kubernetes Service (AKS) zjistíte, jak škálovat uzly a pody v Kubernetes a jak implementovat automatické horizontální škálování podů.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc
ms.openlocfilehash: a268d39ec514fc7b88b555221ece7dc044ca49ba
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767506"
---
# <a name="tutorial-scale-applications-in-azure-kubernetes-service-aks"></a>Kurz: Škálování aplikací ve službě Azure Kubernetes Service (AKS)

Pokud jste postupovali podle kurzů, máte funkční cluster Kubernetes v AKS a nasadili jste ukázkovou hlasovací aplikaci Azure. V tomto kurzu, který je pátou částí sedmidílné série, budete škálovat pody v této aplikaci a vyzkoušíte si jejich automatické škálování. Naučíte se také, jak škálovat počet uzlů virtuálního počítače Azure, aby se změnila kapacita clusteru pro hostování úloh. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Škálování uzlů Kubernetes
> * Ruční škálování podů Kubernetes, ve kterých se spouští vaše aplikace
> * Konfigurace automatického škálování podů, ve kterých se spouští front-end aplikace

V novějších kurzech se aplikace hlasování Azure aktualizuje na novou verzi.

## <a name="before-you-begin"></a>Než začnete

V předchozích kurzech byla aplikace zabalena do image kontejneru. Tato image se nahrála do Azure Container Registry a vytvořili jste cluster AKS. Aplikace se pak nasadí do clusteru AKS. Pokud jste tyto kroky neudělali a chcete je sledovat, začněte s [kurzem 1 – vytváření imagí kontejneru][aks-tutorial-prepare-app].

Tento kurz vyžaduje, abyste spustili Azure CLI verze 2.0.53 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="manually-scale-pods"></a>Ruční škálování podů

Při nasazení front-endu Azure Vote a instance Redis v předchozích kurzech se vytvořila jedna replika. Pokud chcete zobrazit počet a stav podů ve vašem clusteru, použijte příkaz [kubectl get][kubectl-get] následujícím způsobem:

```console
kubectl get pods
```

Následující příklad výstupu ukazuje jeden pod front-endu a jeden pod back-endu:

```output
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Pokud chcete ručně změnit počet podů v nasazení *azure-vote-front*, použijte příkaz [kubectl scale][kubectl-scale]. Následující příklad zvýší počet podů front-endu na *5*:

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

Spusťte [kubectl][kubectl-get] znovu, abyste ověřili, že AKS úspěšně vytvořil další lusky. Po minutě, kdy jsou lusky k dispozici ve vašem clusteru:

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

Kubernetes podporuje [horizontální automatické škálování podů][kubernetes-hpa] pro úpravu počtu podů v nasazení v závislosti na využití procesoru nebo jiné vybrané metrice. K poskytování využití prostředků v Kubernetes se používá [server metrik][metrics-server], který je automaticky nasazený v clusterech AKS verze 1.10 nebo novější. Pokud chcete zobrazit verzi clusteru AKS, použijte příkaz [az aks show][az-aks-show], jak je znázorněno v následujícím příkladu:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query kubernetesVersion --output table
```

> [!NOTE]
> Pokud je váš cluster AKS menší než *1,10*, server metriky se neinstaluje automaticky. Manifesty instalace serveru metriky jsou k dispozici jako `components.yaml` prostředek na vydaných verzích serveru metriky, což znamená, že je můžete nainstalovat přes adresu URL. Další informace o těchto definicích YAML najdete v části věnované [nasazení][metrics-server-github] v souboru Readme.
> 
> Příklad instalace:
> ```console
> kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.3.6/components.yaml
> ```

Chcete-li použít automatické škálování, všechny kontejnery v luskech a vaše lusky musí mít definované požadavky na procesor a omezení. V `azure-vote-front` nasazení má front-end kontejner již požadavky 0,25 procesor s limitem 0,5 CPU. Tyto požadavky na prostředky a omezení jsou definovány tak, jak je znázorněno v následujícím ukázkovém kódu:

```yaml
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

Následující příklad využívá příkaz [kubectl autoscale][kubectl-autoscale] k automatickému škálování počtu podů v nasazení *azure-vote-front*. Pokud průměrné využití procesoru napříč všemi lusky překročí 50% jejich vyžádaného využití, přizpůsobuje se automatické škálování až do maximálního počtu *10* instancí. Pro nasazení je pak definováno minimálně *3* instance:

```console
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Případně můžete vytvořit soubor manifestu, který definuje chování automatického škálování a omezení prostředků. Následuje příklad souboru manifestu s názvem `azure-vote-hpa.yaml` .

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

---

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

Použijte `kubectl apply` k použití automatického škálování definovaného v `azure-vote-hpa.yaml` souboru manifestu.

```console
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

```output
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

V tomto kurzu jste v clusteru Kubernetes využili různé funkce škálování. Naučili jste se:

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
[metrics-server-github]: https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md#deployment
[metrics-server]: https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/#metrics-server

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
