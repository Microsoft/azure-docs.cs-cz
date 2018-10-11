---
title: Kurz služby Azure Container Instances – Příprava služby Azure Container Registry
description: Kurz služby Azure Container Instances, část 2 ze 3 – Příprava služby Azure Container Registry
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: e7cf9c5fad6219e71205c181b2dbb6828595afdd
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48855438"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>Kurz: Nasazení a použití služby Azure Container Registry

Toto je druhá část třídílného kurzu. [V první části](container-instances-tutorial-prepare-app.md) tohoto kurzu se vytvářela image kontejneru Docker pro webovou aplikaci Node.js. V tomto kurzu nahrajete image do služby Azure Container Registry. Pokud jste image kontejneru ještě nevytvořili, vraťte se ke [kurzu 1 – Vytvoření image kontejneru](container-instances-tutorial-prepare-app.md).

Služba Azure Container Registry je vaším privátním registrem Dockeru v Azure. V tomto kurzu vytvoříte ve svém předplatném instanci služby Azure Container Registry a potom do ní odešlete dříve vytvořenou image kontejneru. V tomto článku, který je druhou částí série, se naučíte:

> [!div class="checklist"]
> * Vytvořit instanci služby Azure Container Registry
> * Označit image kontejneru pro svůj registr kontejneru Azure
> * Odeslat image do registru

V následujícím článku, který je posledním dílem série, nasadíte kontejner z privátního registru do služby Azure Container Instances.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="create-azure-container-registry"></a>Vytvoření registru kontejneru Azure

Než vytvoříte registr kontejneru, budete potřebovat *skupinu prostředků*, do které ho budete moct nasadit. Skupina prostředků je logická kolekce, ve které se nasazují a spravují všech prostředky Azure.

Vytvořte skupinu prostředků pomocí příkazu [az group create][az-group-create]. V následujícím příkladu se vytvoří skupina prostředků s názvem *myResourceGroup* v oblasti *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Jakmile vytvoříte skupinu prostředků, vytvořte registr kontejneru Azure pomocí příkazu [az acr create][az-acr-create]. Název registru kontejneru musí být v rámci prostředí Azure jedinečný a musí obsahovat 5 až 50 alfanumerických znaků. Nahraďte položku `<acrName>` jedinečným názvem pro svůj registr:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic --admin-enabled true
```

Zde je příklad výstupu pro nový registr kontejneru Azure s názvem *mycontainerregistry082* (zkrácené zobrazení):

```console
$ az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
...
{
  "adminUserEnabled": true,
  "creationDate": "2018-03-16T21:54:47.297875+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry082",
  "location": "eastus",
  "loginServer": "mycontainerregistry082.azurecr.io",
  "name": "mycontainerregistry082",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Zbývající část tohoto kurzu používá `<acrName>` jako zástupný text pro název registru kontejneru, který zvolíte v tomto kroku.

## <a name="log-in-to-container-registry"></a>Přihlášení k registru kontejneru

Před nahráváním imagí do instance služby Azure Container Registry se k ní musíte přihlásit. Dokončete operaci pomocí příkazu [az acr login][az-acr-login]. Je třeba uvést jedinečný název, který jste zvolili pro registr kontejneru při jeho vytváření.

```azurecli
az acr login --name <acrName>
```

Příkaz po dokončení vrátí zprávu `Login Succeeded` (Přihlášení bylo úspěšné):

```console
$ az acr login --name mycontainerregistry082
Login Succeeded
```

## <a name="tag-container-image"></a>Označení image kontejneru

Pokud chcete image kontejneru odeslat do privátního registru, jako je služba Azure Container Registry, musíte nejprve image označit úplným názvem přihlašovacího serveru registru.

Nejprve získejte úplný název přihlašovacího serveru pro svůj registr kontejneru Azure. Spusťte následující příkaz [az acr show][az-acr-show] a nahraďte zástupný text `<acrName>` názvem registru, který jste právě vytvořili:

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Pokud se například váš registr jmenuje *mycontainerregistry082*, bude příkaz vypadat takto:

```console
$ az acr show --name mycontainerregistry082 --query loginServer --output table
Result
------------------------
mycontainerregistry082.azurecr.io
```

Nyní zobrazte seznam místních imagí pomocí příkazu [docker images][docker-images]:

```bash
docker images
```

Kromě ostatních imagí, které máte na svém počítači, byste měli vidět image *aci-tutorial-app*, které jste vytvořili v [předchozím kurzu](container-instances-tutorial-prepare-app.md):

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 minutes ago    68.1 MB
```

Označte image *aci-tutorial-app* názvem loginServer svého registru kontejneru. Na konec názvu image přidejte také značku `:v1`, abyste označili číslo verze image. Nahraďte položku `<acrLoginServer>` výsledkem příkazu [az acr show][az-acr-show], který jste spustili před chvílí.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

Spusťte příkaz `docker images` znovu, abyste ověřili označení:

```console
$ docker images
REPOSITORY                                            TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app                                      latest    5c745774dfa9    39 minutes ago    68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app    v1        5c745774dfa9    7 minutes ago     68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Nahrání image do služby Azure Container Registry

Teď, když jste image *aci-tutorial-app* označili úplným názvem přihlašovacího serveru pro svůj privátní registr, můžete ji do něj odeslat pomocí příkazu [docker push][docker-push]. Nahraďte položku `<acrLoginServer>` úplným názvem přihlašovacího serveru, který jste získali v dřívějším kroku.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

Operace `push` by měla trvat několik sekund až několik minut v závislosti na připojení k internetu a výstup je podobný následujícímu:

```console
$ docker push mycontainerregistry082.azurecr.io/aci-tutorial-app:v1
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>Vypsání seznamu imagí ve službě Azure Container Registry

Pokud si chcete ověřit, že image, kterou jste právě odeslali, se skutečně nachází v registru kontejneru Azure, použijte příkaz [az acr repository list][az-acr-repository-list] a prohlédněte si seznam imagí ve svém registru. Nahraďte položku `<acrName>` názvem svého registru kontejneru.

```azurecli
az acr repository list --name <acrName> --output table
```

Příklad:

```console
$ az acr repository list --name mycontainerregistry082 --output table
Result
----------------
aci-tutorial-app
```

*Značky* pro konkrétní image si můžete zobrazit pomocí příkazu [az acr repository show-tags][az-acr-repository-show-tags].

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Zobrazený výstup by měl vypadat přibližně takto:

```console
$ az acr repository show-tags --name mycontainerregistry082 --repository aci-tutorial-app --output table
Result
--------
v1
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste připravili registr kontejneru Azure pro použití se službou Azure Container Instances a nahráli jste image kontejneru do registru. Dokončili jste následující kroky:

> [!div class="checklist"]
> * Nasazení instance služby Azure Container Registry
> * Označení image kontejneru pro službu Azure Container Registry
> * Odeslání image do služby Azure Container Registry

Přejděte k dalšímu kurzu, ve kterém se naučíte, jak nasadit kontejner do prostředí Azure s použitím služby Azure Container Instances:

> [!div class="nextstepaction"]
> [Nasazení kontejneru do služby Azure Container Instances](container-instances-tutorial-deploy-app.md)

<!-- LINKS - External -->
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: http://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
