---
title: Kurz Kubernetes v Azure – Nasazení aplikace
description: V tomto kurzu Azure Kubernetes Service (AKS) nasadíte do svého clusteru vícekontejnerovou aplikaci s použitím vlastní image uložené ve službě Azure Container Registry.
services: container-service
ms.topic: tutorial
ms.date: 09/30/2020
ms.custom: mvc
ms.openlocfilehash: 8114aa0b6c2483d543376727a44d14041ed02b37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91576485"
---
# <a name="tutorial-run-applications-in-azure-kubernetes-service-aks"></a>Kurz: Spouštění aplikací ve službě Azure Kubernetes Service (AKS)

Kubernetes poskytuje distribuovanou platformu pro kontejnerizované aplikace. Můžete sestavovat vlastní aplikace a služby a nasazovat je do clusteru Kubernetes, který zajistí správu jejich dostupnosti a možností připojení. V tomto kurzu, který je čtvrtou částí sedmidílné série, se ukázková aplikace nasadí do clusteru Kubernetes. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Aktualizace souboru manifestu Kubernetes
> * Spuštění aplikace v Kubernetes
> * Testování aplikace

V dalších kurzech se tato aplikace škáluje a aktualizuje.

Tento rychlý start předpokládá základní znalosti konceptů Kubernetes. Další informace najdete v tématu [základní koncepty Kubernetes pro Azure Kubernetes Service (AKS)][kubernetes-concepts].

## <a name="before-you-begin"></a>Než začnete

V předchozích kurzech se aplikace zabalila do image kontejneru, tato image se odeslala do Azure Container Registry a vytvořil se cluster Kubernetes.

K dokončení tohoto kurzu potřebujete předem vytvořený soubor manifestu Kubernetes `azure-vote-all-in-one-redis.yaml`. Tento soubor se stáhnul se zdrojovým kódem aplikace v předchozí kurzu. Ověřte, že jste naklonováni úložiště a že jste změnili adresáře do klonovaného úložiště. Pokud jste tyto kroky neudělali a chcete je sledovat, začněte s [kurzem 1 – vytváření imagí kontejneru][aks-tutorial-prepare-app].

Tento kurz vyžaduje, abyste spustili Azure CLI verze 2.0.53 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="update-the-manifest-file"></a>Aktualizace souboru manifestu

V těchto kurzech je image kontejneru pro ukázkovou aplikaci uložená v instanci služby Azure Container Registry (ACR). Pokud chcete aplikaci nasadit, musíte v souboru manifestu Kubernetes aktualizovat název image tak, aby zahrnoval název přihlašovacího serveru ACR.

Název přihlašovacího serveru ACR získáte pomocí příkazu [az acr list][az-acr-list] následujícím způsobem:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

V ukázkovém souboru manifestu z úložiště git naklonovaného v prvním kurzu se jako název přihlašovacího serveru používá *microsoft*. Ujistěte se, že jste naklonováni v adresáři *Azure-hlasování-App-Redis* , a pak otevřete soubor manifestu pomocí textového editoru, jako je například `vi` :

```console
vi azure-vote-all-in-one-redis.yaml
```

Nahraďte *microsoft* názvem vašeho přihlašovacího serveru ACR. Název bitové kopie najdete na řádku 51 souboru manifestu. Následující příklad ukazuje výchozí název image:

```yaml
containers:
- name: azure-vote-front
  image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
```

Zadejte vlastní název přihlašovacího serveru ACR, aby váš soubor manifestu vypadal jako v následujícím příkladu:

```yaml
containers:
- name: azure-vote-front
  image: <acrName>.azurecr.io/azure-vote-front:v1
```

Uložte soubor a zavřete ho. V `vi` použijte `:wq` .

## <a name="deploy-the-application"></a>Nasazení aplikace

Aplikaci nasadíte pomocí příkazu [kubectl apply][kubectl-apply]. Tento příkaz analyzuje soubor manifestu a vytvoří definované objekty Kubernetes. Zadejte ukázkový soubor manifestu, jak je znázorněno v následujícím příkladu:

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

Pomocí příkazu [kubectl get service][kubectl-get] s argumentem `--watch` můžete sledovat průběh.

```console
kubectl get service azure-vote-front --watch
```

Počáteční *IP adresa* pro službu *Azure-hlas-front* je zpočátku zobrazená jako *nevyřízená*:

```
azure-vote-front   LoadBalancer   10.0.34.242   <pending>     80:30676/TCP   5s
```

Pokud se *IP* adresa změní z *čekání* na skutečnou veřejnou IP adresu, použijte `CTRL-C` k zastavení `kubectl` procesu sledování. Následující příklad výstupu ukazuje platnou veřejnou IP adresu přiřazenou ke službě:

```
azure-vote-front   LoadBalancer   10.0.34.242   52.179.23.131   80:30676/TCP   67s
```

Pokud chcete zobrazit aplikaci v akci, otevřete webový prohlížeč na externí IP adresu vaší služby:

![Obrázek clusteru Kubernetes v Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

Pokud se aplikace nenačte, může to být kvůli problému s autorizací u vašeho registru imagí. Stav vašich kontejnerů můžete zobrazit pomocí příkazu `kubectl get pods`. Pokud se image kontejnerů nedají načíst, přečtěte si téma [ověření pomocí Azure Container Registry služby Azure Kubernetes](cluster-container-registry-integration.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste v AKS nasadili ukázkovou aplikaci pro hlasování Azure do clusteru Kubernetes. Naučili jste se:

> [!div class="checklist"]
> * Aktualizace souborů manifestu Kubernetes
> * Spuštění aplikace v Kubernetes
> * Testování aplikace

V dalším kurzu se dozvíte, jak škálovat aplikaci Kubernetes a základní infrastrukturu Kubernetes.

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
