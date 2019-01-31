---
title: Rychlý start – vytvoření privátního registru Dockeru v Azure – Azure CLI
description: Rychle se naučíte, jak vytvořit privátní registr Dockeru pomocí Azure CLI.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, H1Hack27Feb2017, mvc
ms.openlocfilehash: 37b1c8516268611a1174edfe20fef36dfb6b36c2
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55295827"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-cli"></a>Rychlý start: Vytvořit privátní registr pomocí rozhraní příkazového řádku Azure

Azure Container Registry je spravovaná služba registru kontejnerů Dockeru sloužící k ukládání privátních imagí kontejnerů Dockeru. Tato příručka podrobně popisuje vytvoření instance služby Azure Container Registry pomocí Azure CLI. Potom použijte příkazy Dockeru pro nasdílení image kontejneru do registru a nakonec o přijetí změn a spusťte image z registru.

Tento rychlý start vyžaduje použití rozhraní příkazového řádku Azure (verze 2.0.55 nebo později doporučené). Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

Je také nutné mít Docker nainstalovaný místně. Docker nabízí balíčky pro snadnou konfiguraci Dockeru v libovolném systému [macOS][docker-mac], [Windows][docker-windows] nebo [Linux][docker-linux].

Azure Cloud Shell neobsahuje všechny požadované součásti Dockeru (démon `dockerd`), a proto pro tento rychlý start nelze Cloud Shell použít.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create][az-group-create]. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

V tomto rychlém startu vytvoříte *základní* registru, který je možnost náklady optimalizovaná pro vývojáře, další informace o službě Azure Container Registry. Podrobnosti o úrovních služeb najdete v tématu [skladové položky služby Container registry][container-registry-skus].

Pomocí příkazu [az acr create][az-acr-create] vytvořte instanci služby ACR. Název registru musí být jedinečný v rámci Azure a musí obsahovat 5 až 50 alfanumerických znaků. V následujícím příkladu se používá *myContainerRegistry007*. Aktualizujte název na jedinečnou hodnotu.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

Po vytvoření registru je výstup podobný tomuto:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2019-01-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "mycontainerregistry007.azurecr.io",
  "name": "myContainerRegistry007",
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

Poznamenejte si `loginServer` ve výstupu, který je plně kvalifikovaný název registru (malými písmeny). V celé zbývající části tohoto rychlého startu se položka `<acrName>` používá jako zástupný symbol pro název registru kontejneru.

## <a name="log-in-to-registry"></a>Přihlášení k registru

Před odesíláním a vyžadováním imagí kontejnerů, musíte se přihlásit k registru. K tomu použijte příkaz [az acr login][az-acr-login].

```azurecli
az acr login --name <acrName>
```

Příkaz po dokončení vrátí zprávu `Login Succeeded` (Přihlášení bylo úspěšné).

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Výpis imagí kontejnerů

Následující příklad uvádí úložiště v registru:

```azurecli
az acr repository list --name <acrName> --output table
```

Výstup:

```
Result
----------------
busybox
```

Následující příklad vypíše značky v **busybox** úložiště.

```azurecli
az acr repository show-tags --name <acrName> --repository busybox --output table
```

Výstup:

```
Result
--------
v1
```

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete použít [odstranění skupiny az] [ az-group-delete] příkazu odeberte skupinu prostředků, do registru kontejneru a imagí kontejnerů v ní uloženy.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili službu Azure Container Registry pomocí Azure CLI, nahráli image kontejneru do registru a vyžádá a spustili image z registru. Pokračujte na Azure Container Registry kurzy pro najdete podrobnější přehled služby ACR.

> [!div class="nextstepaction"]
> [Kurzy služby Azure Container Registry][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
