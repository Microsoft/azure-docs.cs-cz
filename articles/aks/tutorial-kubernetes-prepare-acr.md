---
title: Kurz Kubernetes v Azure – vytvoření registru kontejnerů
description: V tomto kurzu AKS (Azure Kubernetes Service) vytvoříte instanci Azure Container Registry a nahrajete ukázkovou image kontejneru aplikace.
services: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.custom: mvc
ms.openlocfilehash: 6b8d520a539f69889b1e1bd23d255f3fe19b8717
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374582"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>Kurz: nasazení a použití Azure Container Registry

Azure Container Registry (ACR) je privátní registr pro Image kontejnerů. Privátní registr kontejnerů umožňuje bezpečné sestavení a nasazení aplikací a vlastního kódu. V tomto kurzu, který je druhou částí 7, nasadíte instanci ACR a nahrajete do ní image kontejneru. Naučíte se:

> [!div class="checklist"]
> * Vytvoření instance Azure Container Registry (ACR)
> * Označení image kontejneru pro ACR
> * Nahrání image do ACR
> * Zobrazení imagí v registru

V dalších kurzech se tato instance ACR integruje s clusterem Kubernetes v AKS a z image se nasadí aplikace.

## <a name="before-you-begin"></a>Než začnete

V [předchozím kurzu][aks-tutorial-prepare-app]byla vytvořena image kontejneru pro jednoduchou hlasovací aplikaci v Azure. Pokud jste image hlasovací aplikace v Azure nevytvořili, vraťte se ke [kurzu 1 – vytváření imagí kontejneru][aks-tutorial-prepare-app].

Tento kurz vyžaduje, abyste spustili Azure CLI verze 2.0.53 nebo novější. Pro nalezení verze spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI][azure-cli-install].

## <a name="create-an-azure-container-registry"></a>Vytvoření Azure Container Registry

Pokud chcete vytvořit Azure Container Registry, musíte nejdřív potřebovat skupinu prostředků. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí příkazu [AZ Group Create][az-group-create] . V následujícím příkladu se vytvoří skupina prostředků s názvem *myResourceGroup* v oblasti *eastus* :

```azurecli
az group create --name myResourceGroup --location eastus
```

Pomocí příkazu [AZ ACR Create][az-acr-create] vytvořte instanci Azure Container registry a zadejte vlastní název registru. Název registru musí být v rámci Azure jedinečný a obsahovat 5-50 alfanumerických znaků. Ve zbývající části tohoto kurzu se `<acrName>` používá jako zástupný symbol pro název registru kontejneru. Zadejte vlastní jedinečný název registru. *Základní* SKU je cenově optimalizovaný vstupní bod pro účely vývoje, který poskytuje rovnováhu mezi úložištěm a propustností.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

## <a name="log-in-to-the-container-registry"></a>Přihlášení k registru kontejneru

Pokud chcete použít instanci ACR, musíte se nejdřív přihlásit. Pomocí příkazu [AZ ACR Login][az-acr-login] zadejte jedinečný název, který se v předchozím kroku předává do registru kontejneru.

```azurecli
az acr login --name <acrName>
```

Příkaz po dokončení vrátí zprávu o *úspěšném přihlášení* .

## <a name="tag-a-container-image"></a>Označení image kontejneru

Pokud chcete zobrazit seznam aktuálních místních imagí, použijte příkaz [Docker images][docker-images] :

```
$ docker images

REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Pokud chcete použít image kontejneru *Azure-hlasování* s ACR, musí být image označená adresou přihlašovacího serveru vašeho registru. Tato značka se používá pro směrování při vkládání imagí kontejneru do registru imagí.

Chcete-li získat adresu přihlašovacího serveru, použijte příkaz [AZ ACR list][az-acr-list] a dotaz na *loginServer* následujícím způsobem:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Teď si označíte svůj místní obrázek *Azure-hlasování* s adresou *acrLoginServer* registru kontejneru. Chcete-li určit verzi obrázku, přidejte *: V1* na konec názvu bitové kopie:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Chcete-li ověřit, zda byly značky aplikovány, spusťte znovu [Image Docker][docker-images] . Obrázek je označený adresou instance ACR a číslem verze.

```
$ docker images

REPOSITORY                                           TAG           IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest        eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry.azurecr.io/azure-vote-front      v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest        a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask         788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Vložení imagí do registru

Když jste vytvořili a označili obrázek, nahrajte do instance ACR obrázek *hlasování Azure* . Použijte [Docker push][docker-push] a zadejte vlastní adresu *acrLoginServer* pro název bitové kopie následujícím způsobem:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

Dokončení vložení image do ACR může trvat několik minut.

## <a name="list-images-in-registry"></a>Výpis obrázků v registru

Pokud chcete vrátit seznam imagí, které byly vloženy do instance ACR, použijte příkaz [AZ ACR úložiště list][az-acr-repository-list] . Následujícím způsobem zadejte vlastní `<acrName>`:

```azurecli
az acr repository list --name <acrName> --output table
```

Následující příklad výstupu uvádí obrázek pro *hlasování Azure* , jak je k dispozici v registru:

```
Result
----------------
azure-vote-front
```

Chcete-li zobrazit značky pro konkrétní obrázek, použijte příkaz [AZ ACR úložiště show-Tags][az-acr-repository-show-tags] následujícím způsobem:

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

Následující příklad výstupu ukazuje image *v1* označená v předchozím kroku:

```
Result
--------
v1
```

Nyní máte image kontejneru, která je uložena v privátní instanci Azure Container Registry. Tento obrázek je nasazen z ACR do clusteru Kubernetes v dalším kurzu.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili Azure Container Registry a vložili image pro použití v clusteru AKS. Zjistili jste, jak:

> [!div class="checklist"]
> * Vytvoření instance Azure Container Registry (ACR)
> * Označení image kontejneru pro ACR
> * Nahrání image do ACR
> * Zobrazení imagí v registru

Přejděte k dalšímu kurzu, kde se dozvíte, jak nasadit cluster Kubernetes v Azure.

> [!div class="nextstepaction"]
> [Nasazení clusteru Kubernetes][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr
[az-acr-list]: /cli/azure/acr
[az-acr-login]: https://docs.microsoft.com/cli/azure/acr#az-acr-login
[az-acr-list]: https://docs.microsoft.com/cli/azure/acr#az-acr-list
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
