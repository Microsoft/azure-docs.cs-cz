---
title: Kurz Kubernetes v Azure – nasazení aplikace
description: V tomto kurzu AKS (Azure Kubernetes Service) nasadíte na svůj cluster aplikaci s více kontejnery pomocí vlastní image uložené v Azure Container Registry.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: cc01b12e493f3e0d3cd63786c27819d4704f97f4
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263870"
---
# <a name="tutorial-run-applications-in-azure-kubernetes-service-aks"></a>Kurz: spouštění aplikací ve službě Azure Kubernetes Service (AKS)

Kubernetes poskytuje distribuovanou platformu pro kontejnery aplikací. Sestavíte a nasadíte vlastní aplikace a služby do clusteru Kubernetes a umožníte clusteru spravovat dostupnost a konektivitu. V tomto kurzu, který je čtvrtou částí 7, se ukázková aplikace nasadí do clusteru Kubernetes. Získáte informace o následujících postupech:

> [!div class="checklist"]
> * Aktualizace souboru manifestu Kubernetes
> * Spuštění aplikace v Kubernetes
> * Testování aplikace

V dalších kurzech se tato aplikace škáluje a aktualizuje.

V tomto rychlém startu se předpokládá základní znalost konceptů Kubernetes. Další informace najdete v tématu [základní koncepty Kubernetes pro Azure Kubernetes Service (AKS)][kubernetes-concepts].

## <a name="before-you-begin"></a>Než začnete

V předchozích kurzech byla aplikace zabalená do image kontejneru, tato image se nahrála do Azure Container Registry a vytvořil se cluster Kubernetes.

K dokončení tohoto kurzu potřebujete předem vytvořený soubor manifestu `azure-vote-all-in-one-redis.yaml` Kubernetes. Tento soubor se stáhl pomocí zdrojového kódu aplikace v předchozím kurzu. Ověřte, že jste naklonováni úložiště a že jste změnili adresáře do klonovaného úložiště. Pokud jste tyto kroky neudělali a chcete je sledovat, začněte s [kurzem 1 – vytváření imagí kontejneru][aks-tutorial-prepare-app].

Tento kurz vyžaduje, abyste spustili Azure CLI verze 2.0.53 nebo novější. Pokud chcete zjistit verzi, spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI][azure-cli-install].

## <a name="update-the-manifest-file"></a>Aktualizace souboru manifestu

V těchto kurzech instance Azure Container Registry (ACR) ukládá image kontejneru pro ukázkovou aplikaci. Chcete-li nasadit aplikaci, je nutné aktualizovat název bitové kopie v souboru manifestu Kubernetes tak, aby zahrnoval název přihlašovacího serveru ACR.

Pomocí příkazu [AZ ACR list][az-acr-list] Získejte název přihlašovacího serveru ACR následujícím způsobem:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Vzorový soubor manifestu z úložiště Git, který je klonován v prvním kurzu, používá název přihlašovacího serveru *společnosti Microsoft*. Ujistěte se, že jste naklonováni v adresáři *Azure-hlasování-App-Redis* , a pak otevřete soubor manifestu pomocí textového editoru, jako je například `vi`:

```console
vi azure-vote-all-in-one-redis.yaml
```

Nahraďte *Microsoft* názvem přihlašovacího serveru ACR. Název bitové kopie najdete na řádku 51 souboru manifestu. Následující příklad ukazuje výchozí název obrázku:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Zadejte vlastní název přihlašovacího serveru ACR, aby váš soubor manifestu vypadal jako v následujícím příkladu:

```yaml
containers:
- name: azure-vote-front
  image: <acrName>.azurecr.io/azure-vote-front:v1
```

Soubor uložte a zavřete. V `vi` použijte `:wq`.

## <a name="deploy-the-application"></a>Nasazení aplikace

K nasazení aplikace použijte příkaz [kubectl Apply][kubectl-apply] . Tento příkaz analyzuje soubor manifestu a vytvoří definované objekty Kubernetes. Zadejte Vzorový soubor manifestu, jak je znázorněno v následujícím příkladu:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Následující příklad výstupu ukazuje úspěšné vytvoření prostředků v clusteru AKS:

```
$ kubectl apply -f azure-vote-all-in-one-redis.yaml

deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testování aplikace

Když je aplikace spuštěná, služba Kubernetes zpřístupňuje front-end aplikace na internetu. Dokončení tohoto procesu může trvat několik minut.

Pokud chcete sledovat průběh, použijte příkaz [kubectl Get Service][kubectl-get] s argumentem `--watch`.

```console
kubectl get service azure-vote-front --watch
```

Počáteční *IP adresa* pro službu *Azure-hlas-front* je zpočátku zobrazená jako *nevyřízená*:

```
azure-vote-front   LoadBalancer   10.0.34.242   <pending>     80:30676/TCP   5s
```

Pokud se *IP* adresa změní z *čeká* na skutečnou veřejnou ip adresu, použijte `CTRL-C` a zastavte tak proces kukátka `kubectl`. Následující příklad výstupu ukazuje platnou veřejnou IP adresu přiřazenou ke službě:

```
azure-vote-front   LoadBalancer   10.0.34.242   52.179.23.131   80:30676/TCP   67s
```

Pokud chcete zobrazit aplikaci v akci, otevřete webový prohlížeč na externí IP adresu vaší služby:

![Obrázek clusteru Kubernetes v Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

Pokud se aplikace nenačte, může to být kvůli problému s autorizací u vašeho registru imagí. Chcete-li zobrazit stav kontejnerů, použijte příkaz `kubectl get pods`. Pokud se image kontejnerů nedají načíst, přečtěte si téma [ověření pomocí Azure Container Registry služby Azure Kubernetes](cluster-container-registry-integration.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste v AKS nasadili ukázkovou aplikaci pro hlasování Azure do clusteru Kubernetes. Zjistili jste, jak:

> [!div class="checklist"]
> * Aktualizace souborů manifestu Kubernetes
> * Spuštění aplikace v Kubernetes
> * Testování aplikace

Přejděte k dalšímu kurzu, kde se dozvíte, jak škálovat Kubernetes aplikaci a základní infrastrukturu Kubernetes.

> [!div class="nextstepaction"]
> [Škálování aplikace a infrastruktury Kubernetes][aks-tutorial-scale]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
[kubernetes-concepts]: concepts-clusters-workloads.md
[kubernetes-service]: concepts-network.md#services
