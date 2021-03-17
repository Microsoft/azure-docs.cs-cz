---
title: Kurz – Příprava registru kontejnerů k nasazení bitové kopie
description: Azure Container Instances kurz 2 ze 3 – Příprava služby Azure Container registry a vložení image
ms.topic: tutorial
ms.date: 12/18/2019
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 2eda960c53fc7ba851ffcfbe96bd8e9a48844910
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746945"
---
# <a name="tutorial-create-an-azure-container-registry-and-push-a-container-image"></a>Kurz: vytvoření služby Azure Container registry a vložení image kontejneru

Toto je druhá část třídílného kurzu. [V první části](container-instances-tutorial-prepare-app.md) tohoto kurzu se vytvářela image kontejneru Docker pro webovou aplikaci Node.js. V tomto kurzu nahrajete image do služby Azure Container Registry. Pokud jste image kontejneru ještě nevytvořili, vraťte se ke [kurzu 1 – Vytvoření image kontejneru](container-instances-tutorial-prepare-app.md).

Služba Azure Container Registry je vaším privátním registrem Dockeru v Azure. V tomto kurzu, který je druhou částí série, jste:

> [!div class="checklist"]
> * Vytvoření instance Azure Container Registry pomocí Azure CLI
> * Označit image kontejneru pro svůj registr kontejneru Azure
> * Odeslat image do svého registru

V následujícím článku, který je posledním dílem série, nasadíte kontejner z privátního registru do služby Azure Container Instances.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

[!INCLUDE [container-instances-create-registry](../../includes/container-instances-create-registry.md)]

## <a name="tag-container-image"></a>Označení image kontejneru

Pokud chcete image kontejneru odeslat do privátního registru, jako je služba Azure Container Registry, musíte nejprve image označit úplným názvem přihlašovacího serveru registru.

Nejprve získejte úplný název přihlašovacího serveru pro svůj registr kontejneru Azure. Spusťte následující příkaz [az acr show][az-acr-show] a nahraďte zástupný text `<acrName>` názvem registru, který jste právě vytvořili:

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Pokud se například váš registr jmenuje *mycontainerregistry082* , bude příkaz vypadat takto:

```azurecli
az acr show --name mycontainerregistry082 --query loginServer --output table
```

```output
Result
------------------------
mycontainerregistry082.azurecr.io
```

Nyní zobrazte seznam místních imagí pomocí příkazu [docker images][docker-images]:

```bash
docker images
```

Kromě ostatních imagí, které máte na svém počítači, byste měli vidět image *aci-tutorial-app* , které jste vytvořili v [předchozím kurzu](container-instances-tutorial-prepare-app.md):

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 minutes ago    68.1 MB
```

Označte obrázek *ACI-tutorial-aplikace* pomocí přihlašovacího serveru vašeho registru kontejneru. Na konec názvu image přidejte také značku `:v1`, abyste označili číslo verze image. Nahraďte položku `<acrLoginServer>` výsledkem příkazu [az acr show][az-acr-show], který jste spustili před chvílí.

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

Teď, když jste označili image *ACI-tutorial-App* s úplným názvem přihlašovacího serveru vašeho privátního registru, můžete do registru vložit Image pomocí příkazu [Docker push][docker-push] . Nahraďte položku `<acrLoginServer>` úplným názvem přihlašovacího serveru, který jste získali v dřívějším kroku.

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

```azurecli
az acr repository list --name mycontainerregistry082 --output table
```

```output
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
az acr repository show-tags --name mycontainerregistry082 --repository aci-tutorial-app --output table
Result
--------
v1
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste připravili registr kontejneru Azure pro použití se službou Azure Container Instances a nahráli jste image kontejneru do registru. Dokončili jste následující kroky:

> [!div class="checklist"]
> * Vytvoření instance Azure Container Registry pomocí Azure CLI
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
[nodejs]: https://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
