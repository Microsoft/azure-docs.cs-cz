---
title: Kurz Kubernetes v Azure – Vytvoření registru kontejneru
description: V tomto kurzu Azure Kubernetes Service (AKS) vytvoříte instanci služby Azure Container Registry a nahrajete image kontejneru ukázkové aplikace.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 5089326af1d7f6e057667cd916f35de92bf517ef
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614249"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>Kurz: Nasazení a použití služby Azure Container Registry

Azure Container Registry (ACR) je privátní registr pro Image kontejneru. Privátní registr kontejneru umožňuje zabezpečeně sestavovat a nasazovat aplikace a vlastní kód. V tomto kurzu, který je druhou částí sedmidílné série, nasadíte instanci ACR a odešlete do ní image kontejneru. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření instance služby Azure Container Registry (ACR)
> * Označit image kontejneru pro službu ACR
> * Odeslat image do služby ACR
> * Zobrazení imagí v registru

V dalších kurzech se tato instance služby ACR je integrovaná s clusterem Kubernetes ve službě AKS a nasazení aplikace z image.

## <a name="before-you-begin"></a>Než začnete

V [předchozím kurzu][aks-tutorial-prepare-app] byla vytvořena image kontejneru pro jednoduchou hlasovací aplikaci v Azure. Pokud jste image hlasovací aplikace v Azure ještě nevytvořili, vraťte se ke [kurzu 1 – Vytváření imagí kontejneru][aks-tutorial-prepare-app].

Tento kurz vyžaduje, že používáte Azure CLI verze 2.0.53 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-an-azure-container-registry"></a>Vytvoření služby Azure Container Registry

Pokud chcete vytvořit službu Azure Container Registry, potřebujete nejprve skupinu prostředků. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí příkazu [az group create][az-group-create]. V následujícím příkladu se vytvoří skupina prostředků s názvem *myResourceGroup* v oblasti *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Vytvoření instance služby Azure Container Registry s [az acr vytvořit][az-acr-create] příkaz a zadejte vlastní název registru. Název registru musí být jedinečný v rámci Azure a musí obsahovat 5 až 50 alfanumerických znaků. Ve zbývající části tohoto kurzu se jako zástupný název registru kontejneru používá `<acrName>`. Zadejte název vlastní jedinečné registru. Skladová položka *Basic* představuje vstupní bod optimalizovaný z hlediska nákladů pro účely vývoje a poskytuje vyváženou kombinaci úložiště a propustnosti.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

## <a name="log-in-to-the-container-registry"></a>Přihlášení k registru kontejneru

Pokud chcete používat instanci ACR, musíte se nejprve přihlásit. Použití [az acr login][az-acr-login] příkaz a zadejte jedinečný název zadaný pro registr kontejneru v předchozím kroku.

```azurecli
az acr login --name <acrName>
```

Příkaz po dokončení vrátí zprávu *Login Succeeded* (Přihlášení proběhlo úspěšně).

## <a name="tag-a-container-image"></a>Označení image kontejneru

Chcete-li zobrazit seznam místních imagí aktuální, použijte [imagí dockeru][docker-images] příkaz:

```
$ docker images

REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Pokud chcete použít image kontejneru *azure-vote-front* s ACR, musí být image označená pomocí adresy přihlašovacího serveru vašeho registru. Tato značka se používá pro směrování při nahrávání imagí kontejneru do registru imagí.

Chcete-li získat adresu serveru přihlášení, použijte [az acr list][az-acr-list] příkazů a dotazů pro *loginServer* následujícím způsobem:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Teď označte místní image *azure-vote-front* pomocí adresy *acrloginServer* registru kontejneru. Na konec názvu image přidejte *:v1*, abyste označili číslo verze image:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Chcete-li zkontrolovat, značky se použijí, [imagí dockeru][docker-images] znovu. Image je označená pomocí adresy instance ACR a čísla verze.

```
$ docker images

REPOSITORY                                           TAG           IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest        eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry.azurecr.io/azure-vote-front      v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest        a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask         788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Nahrávání imagí do registru

Vaše Image sestavili a označili, push *azure-vote-front* image do instance služby ACR. Použití [docker push][docker-push] a zadejte vlastní *acrLoginServer* adresa pro název bitové kopie následujícím způsobem:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

Odeslání image do ACR může trvat několik minut.

## <a name="list-images-in-registry"></a>Vypsání imagí v registru

Pokud chcete vrátit seznam imagí, které byly nahrány do instance služby ACR, použijte [az acr úložiště seznamu][az-acr-repository-list] příkazu. Zadejte vlastní hodnotu `<acrName>` následujícím způsobem:

```azurecli
az acr repository list --name <acrName> --output table
```

Následující příklad výstupu jako dostupnou image v registru uvádí *azure-vote-front*:

```
Result
----------------
azure-vote-front
```

Chcete-li zobrazit značky pro konkrétní image, použijte [az acr úložiště show-tags][az-acr-repository-show-tags] takto:

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

Následující příklad výstupu ukazuje image *v1* označenou v předchozím kroku:

```
Result
--------
v1
```

Teď máte image kontejneru uloženou v privátní instanci služby Azure Container Registry. V dalším kurzu se tato image nasadí z ACR do clusteru Kubernetes.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili službu Azure Container Registry a odeslali jste do ní image pro použití v clusteru AKS. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření instance služby Azure Container Registry (ACR)
> * Označit image kontejneru pro službu ACR
> * Odeslat image do služby ACR
> * Zobrazení imagí v registru

V dalším kurzu se dozvíte, jak nasadit cluster Kubernetes v Azure.

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
